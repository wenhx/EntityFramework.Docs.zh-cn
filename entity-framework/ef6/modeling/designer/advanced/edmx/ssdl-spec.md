---
title: SSDL 规范-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: a4af4b1a-40f4-48cc-b2e0-fa8f5d9d5419
ms.openlocfilehash: b20d1f99f1da9c53a8a164fccc461e07d19c879d
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182548"
---
# <a name="ssdl-specification"></a><span data-ttu-id="44652-102">SSDL 规范</span><span class="sxs-lookup"><span data-stu-id="44652-102">SSDL Specification</span></span>
<span data-ttu-id="44652-103">存储架构定义语言 (SSDL) 是一种基于 XML 的语言，用于描述实体框架应用程序的存储模型。</span><span class="sxs-lookup"><span data-stu-id="44652-103">Store schema definition language (SSDL) is an XML-based language that describes the storage model of an Entity Framework application.</span></span>

<span data-ttu-id="44652-104">在实体框架应用程序中，存储模型元数据从 ssdl 文件（使用 SSDL 编写）加载到 StoreItemCollection 的实例中，可以使用中的方法进行访问。"System.object" 类。</span><span class="sxs-lookup"><span data-stu-id="44652-104">In an Entity Framework application, storage model metadata is loaded from a .ssdl file (written in SSDL) into an instance of the System.Data.Metadata.Edm.StoreItemCollection and is accessible by using methods in the System.Data.Metadata.Edm.MetadataWorkspace class.</span></span> <span data-ttu-id="44652-105">实体框架使用存储模型元数据将针对概念模型的查询转换为特定于存储的命令。</span><span class="sxs-lookup"><span data-stu-id="44652-105">Entity Framework uses storage model metadata to translate queries against the conceptual model to store-specific commands.</span></span>

<span data-ttu-id="44652-106">Entity Framework Designer （EF 设计器）在设计时将存储模型信息存储在 .edmx 文件中。</span><span class="sxs-lookup"><span data-stu-id="44652-106">The Entity Framework Designer (EF Designer) stores storage model information in an .edmx file at design time.</span></span> <span data-ttu-id="44652-107">在生成时，Entity Designer 使用 .edmx 文件中的信息来创建运行时实体框架所需的 ssdl 文件。</span><span class="sxs-lookup"><span data-stu-id="44652-107">At build time the Entity Designer uses information in an .edmx file to create the .ssdl file that is needed by Entity Framework at runtime.</span></span>

<span data-ttu-id="44652-108">SSDL 的版本按 XML 命名空间进行区分。</span><span class="sxs-lookup"><span data-stu-id="44652-108">Versions of SSDL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="44652-109">SSDL 版本</span><span class="sxs-lookup"><span data-stu-id="44652-109">SSDL Version</span></span> | <span data-ttu-id="44652-110">XML 命名空间</span><span class="sxs-lookup"><span data-stu-id="44652-110">XML Namespace</span></span>                                     |
|:-------------|:--------------------------------------------------|
| <span data-ttu-id="44652-111">SSDL v1</span><span class="sxs-lookup"><span data-stu-id="44652-111">SSDL v1</span></span>      | https://schemas.microsoft.com/ado/2006/04/edm/ssdl |
| <span data-ttu-id="44652-112">SSDL v2</span><span class="sxs-lookup"><span data-stu-id="44652-112">SSDL v2</span></span>      | https://schemas.microsoft.com/ado/2009/02/edm/ssdl |
| <span data-ttu-id="44652-113">SSDL v3</span><span class="sxs-lookup"><span data-stu-id="44652-113">SSDL v3</span></span>      | https://schemas.microsoft.com/ado/2009/11/edm/ssdl |

## <a name="association-element-ssdl"></a><span data-ttu-id="44652-114">Association 元素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="44652-114">Association Element (SSDL)</span></span>

<span data-ttu-id="44652-115">存储架构定义语言（SSDL）中的**Association**元素指定参与基础数据库中外键约束的表列。</span><span class="sxs-lookup"><span data-stu-id="44652-115">An **Association** element in store schema definition language (SSDL) specifies table columns that participate in a foreign key constraint in the underlying database.</span></span> <span data-ttu-id="44652-116">两个必需的子 End 元素指定位于关联两端的表和各端的重数。</span><span class="sxs-lookup"><span data-stu-id="44652-116">Two required child End elements specify tables at the ends of the association and the multiplicity at each end.</span></span> <span data-ttu-id="44652-117">一个可选的 ReferentialConstraint 元素指定关联的主体端和依赖端以及参与列。</span><span class="sxs-lookup"><span data-stu-id="44652-117">An optional ReferentialConstraint element specifies the principal and dependent ends of the association as well as the participating columns.</span></span> <span data-ttu-id="44652-118">如果不存在**ReferentialConstraint**元素，则必须使用 AssociationSetMapping 元素来指定关联的列映射。</span><span class="sxs-lookup"><span data-stu-id="44652-118">If no **ReferentialConstraint** element is present, an AssociationSetMapping element must be used to specify the column mappings for the association.</span></span>

<span data-ttu-id="44652-119">**Association**元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="44652-119">The **Association** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="44652-120">文档（零个或一个）</span><span class="sxs-lookup"><span data-stu-id="44652-120">Documentation (zero or one)</span></span>
-   <span data-ttu-id="44652-121">结束（正好两个）</span><span class="sxs-lookup"><span data-stu-id="44652-121">End (exactly two)</span></span>
-   <span data-ttu-id="44652-122">ReferentialConstraint （零个或一个）</span><span class="sxs-lookup"><span data-stu-id="44652-122">ReferentialConstraint (zero or one)</span></span>
-   <span data-ttu-id="44652-123">批注元素（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="44652-123">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="44652-124">适用的特性</span><span class="sxs-lookup"><span data-stu-id="44652-124">Applicable Attributes</span></span>

<span data-ttu-id="44652-125">下表介绍可应用于**Association**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="44652-125">The following table describes the attributes that can be applied to the **Association** element.</span></span>

| <span data-ttu-id="44652-126">特性名</span><span class="sxs-lookup"><span data-stu-id="44652-126">Attribute Name</span></span> | <span data-ttu-id="44652-127">是否必需</span><span class="sxs-lookup"><span data-stu-id="44652-127">Is Required</span></span> | <span data-ttu-id="44652-128">值</span><span class="sxs-lookup"><span data-stu-id="44652-128">Value</span></span>                                                                            |
|:---------------|:------------|:---------------------------------------------------------------------------------|
| <span data-ttu-id="44652-129">**Name**</span><span class="sxs-lookup"><span data-stu-id="44652-129">**Name**</span></span>       | <span data-ttu-id="44652-130">是</span><span class="sxs-lookup"><span data-stu-id="44652-130">Yes</span></span>         | <span data-ttu-id="44652-131">基础数据库中相应外键约束的名称。</span><span class="sxs-lookup"><span data-stu-id="44652-131">The name of the corresponding foreign key constraint in the underlying database.</span></span> |

> [!NOTE]
> <span data-ttu-id="44652-132">可以将任意数量的批注特性（自定义 XML 特性）应用于**Association**元素。</span><span class="sxs-lookup"><span data-stu-id="44652-132">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="44652-133">然而，自定义特性可能不属于为 SSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="44652-133">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="44652-134">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="44652-134">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="44652-135">示例</span><span class="sxs-lookup"><span data-stu-id="44652-135">Example</span></span>

<span data-ttu-id="44652-136">下面的示例演示一个**Association**元素，该元素使用**ReferentialConstraint**元素指定参与**FK\_CustomerOrders**外键约束的列：</span><span class="sxs-lookup"><span data-stu-id="44652-136">The following example shows an **Association** element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint:</span></span>

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

## <a name="associationset-element-ssdl"></a><span data-ttu-id="44652-137">AssociationSet 元素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="44652-137">AssociationSet Element (SSDL)</span></span>

<span data-ttu-id="44652-138">存储架构定义语言（SSDL）中的**AssociationSet**元素表示基础数据库中两个表之间的外键约束。</span><span class="sxs-lookup"><span data-stu-id="44652-138">The **AssociationSet** element in store schema definition language (SSDL) represents a foreign key constraint between two tables in the underlying database.</span></span> <span data-ttu-id="44652-139">参与外键约束的表列在 Association 元素中指定。</span><span class="sxs-lookup"><span data-stu-id="44652-139">The table columns that participate in the foreign key constraint are specified in an Association element.</span></span> <span data-ttu-id="44652-140">与给定**associationset**元素对应的**association**元素是在**AssociationSet**元素的**association**特性中指定的。</span><span class="sxs-lookup"><span data-stu-id="44652-140">The **Association** element that corresponds to a given **AssociationSet** element is specified in the **Association** attribute of the **AssociationSet** element.</span></span>

<span data-ttu-id="44652-141">SSDL 关联集通过 AssociationSetMapping 元素映射到 CSDL 关联集。</span><span class="sxs-lookup"><span data-stu-id="44652-141">SSDL association sets are mapped to CSDL association sets by an AssociationSetMapping element.</span></span> <span data-ttu-id="44652-142">但是，如果使用 ReferentialConstraint 元素定义给定 CSDL 关联集的 CSDL 关联，则不需要相应的**AssociationSetMapping**元素。</span><span class="sxs-lookup"><span data-stu-id="44652-142">However, if the CSDL association for a given CSDL association set is defined by using a ReferentialConstraint element , no corresponding **AssociationSetMapping** element is necessary.</span></span> <span data-ttu-id="44652-143">在这种情况下，如果存在**AssociationSetMapping**元素，该元素定义的映射将被**ReferentialConstraint**元素重写。</span><span class="sxs-lookup"><span data-stu-id="44652-143">In this case, if an **AssociationSetMapping** element is present, the mappings it defines will be overridden by the **ReferentialConstraint** element.</span></span>

<span data-ttu-id="44652-144">**AssociationSet**元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="44652-144">The **AssociationSet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="44652-145">文档（零个或一个）</span><span class="sxs-lookup"><span data-stu-id="44652-145">Documentation (zero or one)</span></span>
-   <span data-ttu-id="44652-146">End（零个或两个）</span><span class="sxs-lookup"><span data-stu-id="44652-146">End (zero or two)</span></span>
-   <span data-ttu-id="44652-147">批注元素（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="44652-147">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="44652-148">适用的特性</span><span class="sxs-lookup"><span data-stu-id="44652-148">Applicable Attributes</span></span>

<span data-ttu-id="44652-149">下表介绍可应用于**AssociationSet**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="44652-149">The following table describes the attributes that can be applied to the **AssociationSet** element.</span></span>

| <span data-ttu-id="44652-150">特性名</span><span class="sxs-lookup"><span data-stu-id="44652-150">Attribute Name</span></span>  | <span data-ttu-id="44652-151">是否必需</span><span class="sxs-lookup"><span data-stu-id="44652-151">Is Required</span></span> | <span data-ttu-id="44652-152">值</span><span class="sxs-lookup"><span data-stu-id="44652-152">Value</span></span>                                                                                                |
|:----------------|:------------|:-----------------------------------------------------------------------------------------------------|
| <span data-ttu-id="44652-153">**Name**</span><span class="sxs-lookup"><span data-stu-id="44652-153">**Name**</span></span>        | <span data-ttu-id="44652-154">是</span><span class="sxs-lookup"><span data-stu-id="44652-154">Yes</span></span>         | <span data-ttu-id="44652-155">关联集表示的外键约束的名称。</span><span class="sxs-lookup"><span data-stu-id="44652-155">The name of the foreign key constraint that the association set represents.</span></span>                          |
| <span data-ttu-id="44652-156">**关联**</span><span class="sxs-lookup"><span data-stu-id="44652-156">**Association**</span></span> | <span data-ttu-id="44652-157">是</span><span class="sxs-lookup"><span data-stu-id="44652-157">Yes</span></span>         | <span data-ttu-id="44652-158">定义参与外键约束的列的关联的名称。</span><span class="sxs-lookup"><span data-stu-id="44652-158">The name of the association that defines the columns that participate in the foreign key constraint.</span></span> |

> [!NOTE]
> <span data-ttu-id="44652-159">可以将任意数量的批注特性（自定义 XML 特性）应用于**AssociationSet**元素。</span><span class="sxs-lookup"><span data-stu-id="44652-159">Any number of annotation attributes (custom XML attributes) may be applied to the **AssociationSet** element.</span></span> <span data-ttu-id="44652-160">然而，自定义特性可能不属于为 SSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="44652-160">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="44652-161">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="44652-161">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="44652-162">示例</span><span class="sxs-lookup"><span data-stu-id="44652-162">Example</span></span>

<span data-ttu-id="44652-163">下面的示例演示了一个**AssociationSet**元素，该元素表示基础数据库中的 `FK_CustomerOrders` foreign key 约束：</span><span class="sxs-lookup"><span data-stu-id="44652-163">The following example shows an **AssociationSet** element that represents the `FK_CustomerOrders` foreign key constraint in the underlying database:</span></span>

``` xml
 <AssociationSet Name="FK_CustomerOrders"
                 Association="ExampleModel.Store.FK_CustomerOrders">
   <End Role="Customers" EntitySet="Customers" />
   <End Role="Orders" EntitySet="Orders" />
 </AssociationSet>
```

## <a name="collectiontype-element-ssdl"></a><span data-ttu-id="44652-164">CollectionType 元素（SSDL）</span><span class="sxs-lookup"><span data-stu-id="44652-164">CollectionType Element (SSDL)</span></span>

<span data-ttu-id="44652-165">存储架构定义语言（SSDL）中的**CollectionType**元素指定函数的返回类型是集合。</span><span class="sxs-lookup"><span data-stu-id="44652-165">The **CollectionType** element in store schema definition language (SSDL) specifies that a function’s return type is a collection.</span></span> <span data-ttu-id="44652-166">**CollectionType**元素是 ReturnType 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="44652-166">The **CollectionType** element is a child of the ReturnType element.</span></span> <span data-ttu-id="44652-167">集合的类型是通过使用 RowType 子元素指定的：</span><span class="sxs-lookup"><span data-stu-id="44652-167">The type of collection is specified by using the RowType child element:</span></span>

> [!NOTE]
> <span data-ttu-id="44652-168">可以将任意数量的批注特性（自定义 XML 特性）应用于**CollectionType**元素。</span><span class="sxs-lookup"><span data-stu-id="44652-168">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="44652-169">然而，自定义特性可能不属于为 SSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="44652-169">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="44652-170">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="44652-170">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="44652-171">示例</span><span class="sxs-lookup"><span data-stu-id="44652-171">Example</span></span>

<span data-ttu-id="44652-172">下面的示例演示一个函数，该函数使用**CollectionType**元素来指定该函数返回行的集合。</span><span class="sxs-lookup"><span data-stu-id="44652-172">The following example shows a function that uses a **CollectionType** element to specify that the function returns a collection of rows.</span></span>

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

## <a name="commandtext-element-ssdl"></a><span data-ttu-id="44652-173">CommandText 元素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="44652-173">CommandText Element (SSDL)</span></span>

<span data-ttu-id="44652-174">存储架构定义语言（SSDL）中的**CommandText**元素是 Function 元素的子元素，它允许您定义在数据库中执行的 SQL 语句。</span><span class="sxs-lookup"><span data-stu-id="44652-174">The **CommandText** element in store schema definition language (SSDL) is a child of the Function element that allows you to define a SQL statement that is executed at the database.</span></span> <span data-ttu-id="44652-175">**CommandText**元素允许添加类似于数据库中的存储过程的功能，但在存储模型中定义**CommandText**元素。</span><span class="sxs-lookup"><span data-stu-id="44652-175">The **CommandText** element allows you to add functionality that is similar to a stored procedure in the database, but you define the **CommandText** element in the storage model.</span></span>

<span data-ttu-id="44652-176">**CommandText**元素不能有子元素。</span><span class="sxs-lookup"><span data-stu-id="44652-176">The **CommandText** element cannot have child elements.</span></span> <span data-ttu-id="44652-177">**CommandText**元素的正文必须是基础数据库的有效 SQL 语句。</span><span class="sxs-lookup"><span data-stu-id="44652-177">The body of the **CommandText** element must be a valid SQL statement for the underlying database.</span></span>

<span data-ttu-id="44652-178">无特性适用于**CommandText**元素。</span><span class="sxs-lookup"><span data-stu-id="44652-178">No attributes are applicable to the **CommandText** element.</span></span>

### <a name="example"></a><span data-ttu-id="44652-179">示例</span><span class="sxs-lookup"><span data-stu-id="44652-179">Example</span></span>

<span data-ttu-id="44652-180">下面的示例演示具有子**CommandText**元素的**函数**元素。</span><span class="sxs-lookup"><span data-stu-id="44652-180">The following example shows a **Function** element with a child **CommandText** element.</span></span> <span data-ttu-id="44652-181">通过将**UpdateProductInOrder**函数导入到概念模型中，将其作为 ObjectContext 的方法公开。</span><span class="sxs-lookup"><span data-stu-id="44652-181">Expose the **UpdateProductInOrder** function as a method on the ObjectContext by importing it into the conceptual model.</span></span>  

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

## <a name="definingquery-element-ssdl"></a><span data-ttu-id="44652-182">DefiningQuery 元素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="44652-182">DefiningQuery Element (SSDL)</span></span>

<span data-ttu-id="44652-183">使用存储架构定义语言（SSDL）中的**DefiningQuery**元素，可以直接在基础数据库中执行 SQL 语句。</span><span class="sxs-lookup"><span data-stu-id="44652-183">The **DefiningQuery** element in store schema definition language (SSDL) allows you to execute a SQL statement directly in the underlying database.</span></span> <span data-ttu-id="44652-184">**DefiningQuery**元素通常用于数据库视图，但该视图是在存储模型中定义的，而不是在数据库中定义的。</span><span class="sxs-lookup"><span data-stu-id="44652-184">The **DefiningQuery** element is commonly used like a database view, but the view is defined in the storage model instead of the database.</span></span> <span data-ttu-id="44652-185">在**DefiningQuery**元素中定义的视图可以通过 EntitySetMapping 元素映射到概念模型中的实体类型。</span><span class="sxs-lookup"><span data-stu-id="44652-185">The view defined in a **DefiningQuery** element can be mapped to an entity type in the conceptual model through an EntitySetMapping element.</span></span> <span data-ttu-id="44652-186">这些映射是只读的。</span><span class="sxs-lookup"><span data-stu-id="44652-186">These mappings are read-only.</span></span>  

<span data-ttu-id="44652-187">下面的 SSDL 语法显示了**EntitySet**的声明，后跟包含用于检索视图的查询的**DefiningQuery**元素。</span><span class="sxs-lookup"><span data-stu-id="44652-187">The following SSDL syntax shows the declaration of an **EntitySet** followed by the **DefiningQuery** element that contains a query used to retrieve the view.</span></span>

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

<span data-ttu-id="44652-188">您可以使用实体框架中的存储过程来对视图启用读写方案。</span><span class="sxs-lookup"><span data-stu-id="44652-188">You can use stored procedures in the Entity Framework to enable read-write scenarios over views.</span></span><span data-ttu-id="44652-189"> 您可以使用数据源视图或实体 SQL 视图作为基表来检索数据和存储过程的更改处理。</span><span class="sxs-lookup"><span data-stu-id="44652-189"> You can use either a data source view or an Entity SQL view as the base table for retrieving data and for change processing by stored procedures.</span></span>

<span data-ttu-id="44652-190">你可以使用**DefiningQuery**元素将 Microsoft SQL Server Compact 为3.5。</span><span class="sxs-lookup"><span data-stu-id="44652-190">You can use the **DefiningQuery** element to target Microsoft SQL Server Compact 3.5.</span></span> <span data-ttu-id="44652-191">尽管 SQL Server Compact 3.5 不支持存储过程，但你可以通过**DefiningQuery**元素实现类似功能。</span><span class="sxs-lookup"><span data-stu-id="44652-191">Though SQL Server Compact 3.5 does not support stored procedures, you can implement similar functionality with the **DefiningQuery** element.</span></span> <span data-ttu-id="44652-192">另一个有用之处在于，可以创建存储过程以克服编程语言中使用的数据类型与数据源的数据类型的不匹配。</span><span class="sxs-lookup"><span data-stu-id="44652-192">Another place where it can be useful is in creating stored procedures to overcome a mismatch between the data types used in the programming language and those of the data source.</span></span> <span data-ttu-id="44652-193">您可以编写一个使用一组特定参数的**DefiningQuery** ，然后使用一组不同的参数调用存储过程，例如，删除数据的存储过程。</span><span class="sxs-lookup"><span data-stu-id="44652-193">You could write a **DefiningQuery** that takes a certain set of parameters and then calls a stored procedure with a different set of parameters, for example, a stored procedure that deletes data.</span></span>

## <a name="dependent-element-ssdl"></a><span data-ttu-id="44652-194">Dependent 元素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="44652-194">Dependent Element (SSDL)</span></span>

<span data-ttu-id="44652-195">存储架构定义语言（SSDL）中的**依赖**元素是 ReferentialConstraint 元素的子元素，用于定义外键约束（也称为 "引用约束"）的依赖端。</span><span class="sxs-lookup"><span data-stu-id="44652-195">The **Dependent** element in store schema definition language (SSDL) is a child element to the ReferentialConstraint element that defines the dependent end of a foreign key constraint (also called a referential constraint).</span></span> <span data-ttu-id="44652-196">**Dependent**元素指定引用主键列（或列）的表中的一个或多个列。</span><span class="sxs-lookup"><span data-stu-id="44652-196">The **Dependent** element specifies the column (or columns) in a table that reference a primary key column (or columns).</span></span> <span data-ttu-id="44652-197">**PropertyRef**元素指定要引用的列。</span><span class="sxs-lookup"><span data-stu-id="44652-197">**PropertyRef** elements specify which columns are referenced.</span></span> <span data-ttu-id="44652-198">主体元素指定**依赖**元素中指定的列所引用的主键列。</span><span class="sxs-lookup"><span data-stu-id="44652-198">The Principal element specifies the primary key columns that are referenced by columns that are specified in the **Dependent** element.</span></span>

<span data-ttu-id="44652-199">**依赖**元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="44652-199">The **Dependent** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="44652-200">PropertyRef（一个或多个）</span><span class="sxs-lookup"><span data-stu-id="44652-200">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="44652-201">批注元素（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="44652-201">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="44652-202">适用的特性</span><span class="sxs-lookup"><span data-stu-id="44652-202">Applicable Attributes</span></span>

<span data-ttu-id="44652-203">下表介绍可应用于**依赖**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="44652-203">The following table describes the attributes that can be applied to the **Dependent** element.</span></span>

| <span data-ttu-id="44652-204">特性名</span><span class="sxs-lookup"><span data-stu-id="44652-204">Attribute Name</span></span> | <span data-ttu-id="44652-205">是否必需</span><span class="sxs-lookup"><span data-stu-id="44652-205">Is Required</span></span> | <span data-ttu-id="44652-206">值</span><span class="sxs-lookup"><span data-stu-id="44652-206">Value</span></span>                                                                                                                                                       |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="44652-207">**角色**</span><span class="sxs-lookup"><span data-stu-id="44652-207">**Role**</span></span>       | <span data-ttu-id="44652-208">是</span><span class="sxs-lookup"><span data-stu-id="44652-208">Yes</span></span>         | <span data-ttu-id="44652-209">与相应的 End 元素的**Role**特性（如果使用）相同的值;否则为包含引用列的表的名称。</span><span class="sxs-lookup"><span data-stu-id="44652-209">The same value as the **Role** attribute (if used) of the corresponding End element; otherwise, the name of the table that contains the referencing column.</span></span> |

> [!NOTE]
> <span data-ttu-id="44652-210">可以将任意数量的批注特性（自定义 XML 特性）应用于**依赖**元素。</span><span class="sxs-lookup"><span data-stu-id="44652-210">Any number of annotation attributes (custom XML attributes) may be applied to the **Dependent** element.</span></span> <span data-ttu-id="44652-211">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="44652-211">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="44652-212">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="44652-212">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="44652-213">示例</span><span class="sxs-lookup"><span data-stu-id="44652-213">Example</span></span>

<span data-ttu-id="44652-214">下面的示例演示一个 Association 元素，该元素使用**ReferentialConstraint**元素指定参与**FK\_CustomerOrders**外键约束的列。</span><span class="sxs-lookup"><span data-stu-id="44652-214">The following example shows an Association element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="44652-215">**Dependent**元素将**Order**表的**CustomerId**列指定为约束的依赖端。</span><span class="sxs-lookup"><span data-stu-id="44652-215">The **Dependent** element specifies the **CustomerId** column of the **Order** table as the dependent end of the constraint.</span></span>

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

## <a name="documentation-element-ssdl"></a><span data-ttu-id="44652-216">Documentation 元素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="44652-216">Documentation Element (SSDL)</span></span>

<span data-ttu-id="44652-217">存储架构定义语言（SSDL）中的**文档**元素可用于提供有关在父元素中定义的对象的信息。</span><span class="sxs-lookup"><span data-stu-id="44652-217">The **Documentation** element in store schema definition language (SSDL) can be used to provide information about an object that is defined in a parent element.</span></span>

<span data-ttu-id="44652-218">**文档**元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="44652-218">The **Documentation** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="44652-219">**摘要**：父元素的简要说明。</span><span class="sxs-lookup"><span data-stu-id="44652-219">**Summary**: A brief description of the parent element.</span></span> <span data-ttu-id="44652-220">（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="44652-220">(zero or one element)</span></span>
-   <span data-ttu-id="44652-221">**LongDescription**：父元素的详细说明。</span><span class="sxs-lookup"><span data-stu-id="44652-221">**LongDescription**: An extensive description of the parent element.</span></span> <span data-ttu-id="44652-222">（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="44652-222">(zero or one element)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="44652-223">适用的特性</span><span class="sxs-lookup"><span data-stu-id="44652-223">Applicable Attributes</span></span>

<span data-ttu-id="44652-224">可以将任意数量的批注特性（自定义 XML 特性）应用于**文档**元素。</span><span class="sxs-lookup"><span data-stu-id="44652-224">Any number of annotation attributes (custom XML attributes) may be applied to the **Documentation** element.</span></span> <span data-ttu-id="44652-225">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="44652-225">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="44652-226">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="44652-226">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="44652-227">示例</span><span class="sxs-lookup"><span data-stu-id="44652-227">Example</span></span>

<span data-ttu-id="44652-228">下面的示例将**文档**元素显示为 EntityType 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="44652-228">The following example shows the **Documentation** element as a child element of an EntityType element.</span></span>

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

## <a name="end-element-ssdl"></a><span data-ttu-id="44652-229">End 元素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="44652-229">End Element (SSDL)</span></span>

<span data-ttu-id="44652-230">以存储架构定义语言（SSDL）表示的**结束**元素指定基础数据库中外键约束一端的表和行数。</span><span class="sxs-lookup"><span data-stu-id="44652-230">The **End** element in store schema definition language (SSDL) specifies the table and number of rows at one end of a foreign key constraint in the underlying database.</span></span> <span data-ttu-id="44652-231">**End**元素可以是 Association 元素或 AssociationSet 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="44652-231">The **End** element can be a child of the Association element or the AssociationSet element.</span></span> <span data-ttu-id="44652-232">在每种情况下，可能的子元素以及适用的特性都不同。</span><span class="sxs-lookup"><span data-stu-id="44652-232">In each case, the possible child elements and applicable attributes are different.</span></span>

### <a name="end-element-as-a-child-of-the-association-element"></a><span data-ttu-id="44652-233">End 元素作为 Association 元素的子元素</span><span class="sxs-lookup"><span data-stu-id="44652-233">End Element as a Child of the Association Element</span></span>

<span data-ttu-id="44652-234">**End**元素（作为**Association**元素的子元素）使用**Type**和**多重性**特性指定 foreign key 约束末尾的表和行数。</span><span class="sxs-lookup"><span data-stu-id="44652-234">An **End** element (as a child of the **Association** element) specifies the table and number of rows at the end of a foreign key constraint with the **Type** and **Multiplicity** attributes respectively.</span></span> <span data-ttu-id="44652-235">外键约束的两端定义为 SSDL 关联的一部分；SSDL 关联必须仅具有两端。</span><span class="sxs-lookup"><span data-stu-id="44652-235">Ends of a foreign key constraint are defined as part of an SSDL association; an SSDL association must have exactly two ends.</span></span>

<span data-ttu-id="44652-236">**结束**元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="44652-236">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="44652-237">文档（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="44652-237">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="44652-238">OnDelete （零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="44652-238">OnDelete (zero or one element)</span></span>
-   <span data-ttu-id="44652-239">批注元素（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="44652-239">Annotation elements (zero or more elements)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="44652-240">适用的特性</span><span class="sxs-lookup"><span data-stu-id="44652-240">Applicable Attributes</span></span>

<span data-ttu-id="44652-241">下表描述了当**结束**元素是**Association**元素的子元素时，可应用于该元素的特性。</span><span class="sxs-lookup"><span data-stu-id="44652-241">The following table describes the attributes that can be applied to the **End** element when it is the child of an **Association** element.</span></span>

| <span data-ttu-id="44652-242">特性名</span><span class="sxs-lookup"><span data-stu-id="44652-242">Attribute Name</span></span>   | <span data-ttu-id="44652-243">是否必需</span><span class="sxs-lookup"><span data-stu-id="44652-243">Is Required</span></span> | <span data-ttu-id="44652-244">值</span><span class="sxs-lookup"><span data-stu-id="44652-244">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                      |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="44652-245">类型</span><span class="sxs-lookup"><span data-stu-id="44652-245">**Type**</span></span>         | <span data-ttu-id="44652-246">是</span><span class="sxs-lookup"><span data-stu-id="44652-246">Yes</span></span>         | <span data-ttu-id="44652-247">处于外键约束端的 SSDL 实体集的完全限定名。</span><span class="sxs-lookup"><span data-stu-id="44652-247">The fully qualified name of the SSDL entity set that is at the end of the foreign key constraint.</span></span>                                                                                                                                                                                                                                                                                          |
| <span data-ttu-id="44652-248">**角色**</span><span class="sxs-lookup"><span data-stu-id="44652-248">**Role**</span></span>         | <span data-ttu-id="44652-249">是</span><span class="sxs-lookup"><span data-stu-id="44652-249">No</span></span>          | <span data-ttu-id="44652-250">相应 ReferentialConstraint 元素（如果使用）的主体或从属元素中的**Role**特性的值。</span><span class="sxs-lookup"><span data-stu-id="44652-250">The value of the **Role** attribute in either the Principal or Dependent element of the corresponding ReferentialConstraint element (if used).</span></span>                                                                                                                                                                                                                                             |
| <span data-ttu-id="44652-251">**多样性**</span><span class="sxs-lookup"><span data-stu-id="44652-251">**Multiplicity**</span></span> | <span data-ttu-id="44652-252">是</span><span class="sxs-lookup"><span data-stu-id="44652-252">Yes</span></span>         | <span data-ttu-id="44652-253">**1**、 **0、1**或 **\*** ，具体取决于外键约束末尾的行数。</span><span class="sxs-lookup"><span data-stu-id="44652-253">**1**, **0..1**, or **\*** depending on the number of rows that can be at the end of the foreign key constraint.</span></span> <br/> <span data-ttu-id="44652-254">**1**指示在外键约束端刚好存在一行。</span><span class="sxs-lookup"><span data-stu-id="44652-254">**1** indicates that exactly one row exists at the foreign key constraint end.</span></span> <br/> <span data-ttu-id="44652-255">**0 .0**表示外键约束端处存在零个或一行。</span><span class="sxs-lookup"><span data-stu-id="44652-255">**0..1** indicates that zero or one row exists at the foreign key constraint end.</span></span> <br/> <span data-ttu-id="44652-256">**\*** 指示外键约束端处存在零行、一行或多行。</span><span class="sxs-lookup"><span data-stu-id="44652-256">**\*** indicates that zero, one, or more rows exist at the foreign key constraint end.</span></span> |

> [!NOTE]
> <span data-ttu-id="44652-257">可以将任意数量的批注特性（自定义 XML 特性）应用于**结束**元素。</span><span class="sxs-lookup"><span data-stu-id="44652-257">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="44652-258">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="44652-258">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="44652-259">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="44652-259">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

#### <a name="example"></a><span data-ttu-id="44652-260">示例</span><span class="sxs-lookup"><span data-stu-id="44652-260">Example</span></span>

<span data-ttu-id="44652-261">下面的示例演示了一个**Association**元素，该元素定义了**FK\_CustomerOrders**外键约束。</span><span class="sxs-lookup"><span data-stu-id="44652-261">The following example shows an **Association** element that defines the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="44652-262">在每个**End**元素上指定的**多重性**值指示**Orders**表中的多个行可以与**Customers**表中的某一行相关联，但**customers**表中只有一个行可以与**Orders**表中的一行相关联。</span><span class="sxs-lookup"><span data-stu-id="44652-262">The **Multiplicity** values specified on each **End** element indicate that many rows in the **Orders** table can be associated with a row in the **Customers** table, but only one row in the **Customers** table can be associated with a row in the **Orders** table.</span></span> <span data-ttu-id="44652-263">此外， **OnDelete**元素指示如果删除**customers**表中的行，则将删除**Orders**表中引用**customers**表中的特定行的所有行。</span><span class="sxs-lookup"><span data-stu-id="44652-263">Additionally, the **OnDelete** element indicates that all rows in the **Orders** table that reference a particular row in the **Customers** table will be deleted if the row in the **Customers** table is deleted.</span></span>

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

### <a name="end-element-as-a-child-of-the-associationset-element"></a><span data-ttu-id="44652-264">End 元素作为 AssociationSet 元素的子元素</span><span class="sxs-lookup"><span data-stu-id="44652-264">End Element as a Child of the AssociationSet Element</span></span>

<span data-ttu-id="44652-265">**End**元素（作为**AssociationSet**元素的子元素的子元素）指定基础数据库中外键约束一端的表。</span><span class="sxs-lookup"><span data-stu-id="44652-265">The **End** element (as a child of the **AssociationSet** element) specifies a table at one end of a foreign key constraint in the underlying database.</span></span>

<span data-ttu-id="44652-266">**结束**元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="44652-266">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="44652-267">文档（零个或一个）</span><span class="sxs-lookup"><span data-stu-id="44652-267">Documentation (zero or one)</span></span>
-   <span data-ttu-id="44652-268">批注元素（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="44652-268">Annotation elements (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="44652-269">适用的特性</span><span class="sxs-lookup"><span data-stu-id="44652-269">Applicable Attributes</span></span>

<span data-ttu-id="44652-270">下表描述了当**结束**元素为**AssociationSet**元素的子元素时，可应用于该元素的特性。</span><span class="sxs-lookup"><span data-stu-id="44652-270">The following table describes the attributes that can be applied to the **End** element when it is the child of an **AssociationSet** element.</span></span>

| <span data-ttu-id="44652-271">特性名</span><span class="sxs-lookup"><span data-stu-id="44652-271">Attribute Name</span></span> | <span data-ttu-id="44652-272">是否必需</span><span class="sxs-lookup"><span data-stu-id="44652-272">Is Required</span></span> | <span data-ttu-id="44652-273">值</span><span class="sxs-lookup"><span data-stu-id="44652-273">Value</span></span>                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="44652-274">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="44652-274">**EntitySet**</span></span>  | <span data-ttu-id="44652-275">是</span><span class="sxs-lookup"><span data-stu-id="44652-275">Yes</span></span>         | <span data-ttu-id="44652-276">处于外键约束一端的 SSDL 实体集的名称。</span><span class="sxs-lookup"><span data-stu-id="44652-276">The name of the SSDL entity set that is at the end of the foreign key constraint.</span></span>                                      |
| <span data-ttu-id="44652-277">**角色**</span><span class="sxs-lookup"><span data-stu-id="44652-277">**Role**</span></span>       | <span data-ttu-id="44652-278">是</span><span class="sxs-lookup"><span data-stu-id="44652-278">No</span></span>          | <span data-ttu-id="44652-279">在对应的 Association 元素的一个**End**元素上指定的一个**角色**特性的值。</span><span class="sxs-lookup"><span data-stu-id="44652-279">The value of one of the **Role** attributes specified on one **End** element of the corresponding Association element.</span></span> |

> [!NOTE]
> <span data-ttu-id="44652-280">可以将任意数量的批注特性（自定义 XML 特性）应用于**结束**元素。</span><span class="sxs-lookup"><span data-stu-id="44652-280">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="44652-281">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="44652-281">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="44652-282">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="44652-282">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

#### <a name="example"></a><span data-ttu-id="44652-283">示例</span><span class="sxs-lookup"><span data-stu-id="44652-283">Example</span></span>

<span data-ttu-id="44652-284">下面的示例演示一个具有**AssociationSet**元素的**EntityContainer**元素，其中包含两个**End**元素：</span><span class="sxs-lookup"><span data-stu-id="44652-284">The following example shows an **EntityContainer** element with an **AssociationSet** element with two **End** elements:</span></span>

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

## <a name="entitycontainer-element-ssdl"></a><span data-ttu-id="44652-285">EntityContainer 元素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="44652-285">EntityContainer Element (SSDL)</span></span>

<span data-ttu-id="44652-286">存储架构定义语言（SSDL）中的**EntityContainer**元素描述实体框架应用程序中的基础数据源的结构： ssdl 实体集（在 EntitySet 元素中定义）表示数据库中的表，ssdl 实体类型（在 EntityType 元素中定义）表示表中的行，关联集（在 AssociationSet 元素中定义）表示数据库中的外键约束。</span><span class="sxs-lookup"><span data-stu-id="44652-286">An **EntityContainer** element in store schema definition language (SSDL) describes the structure of the underlying data source in an Entity Framework application: SSDL entity sets (defined in EntitySet elements) represent tables in a database, SSDL entity types (defined in EntityType elements) represent rows in a table, and association sets (defined in AssociationSet elements) represent foreign key constraints in a database.</span></span> <span data-ttu-id="44652-287">存储模型实体容器通过 EntityContainerMapping 元素映射到概念模型实体容器。</span><span class="sxs-lookup"><span data-stu-id="44652-287">A storage model entity container maps to a conceptual model entity container through the EntityContainerMapping element.</span></span>

<span data-ttu-id="44652-288">**EntityContainer**元素可以有零个或一个文档元素。</span><span class="sxs-lookup"><span data-stu-id="44652-288">An **EntityContainer** element can have zero or one Documentation elements.</span></span> <span data-ttu-id="44652-289">如果存在**文档**元素，则它必须位于所有其他子元素之前。</span><span class="sxs-lookup"><span data-stu-id="44652-289">If a **Documentation** element is present, it must precede all other child elements.</span></span>

<span data-ttu-id="44652-290">**EntityContainer**元素可包含零个或多个下列子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="44652-290">An **EntityContainer** element can have zero or more of the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="44652-291">EntitySet</span><span class="sxs-lookup"><span data-stu-id="44652-291">EntitySet</span></span>
-   <span data-ttu-id="44652-292">AssociationSet</span><span class="sxs-lookup"><span data-stu-id="44652-292">AssociationSet</span></span>
-   <span data-ttu-id="44652-293">批注元素</span><span class="sxs-lookup"><span data-stu-id="44652-293">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="44652-294">适用的特性</span><span class="sxs-lookup"><span data-stu-id="44652-294">Applicable Attributes</span></span>

<span data-ttu-id="44652-295">下表介绍可应用于**EntityContainer**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="44652-295">The table below describes the attributes that can be applied to the **EntityContainer** element.</span></span>

| <span data-ttu-id="44652-296">特性名</span><span class="sxs-lookup"><span data-stu-id="44652-296">Attribute Name</span></span> | <span data-ttu-id="44652-297">是否必需</span><span class="sxs-lookup"><span data-stu-id="44652-297">Is Required</span></span> | <span data-ttu-id="44652-298">值</span><span class="sxs-lookup"><span data-stu-id="44652-298">Value</span></span>                                                                   |
|:---------------|:------------|:------------------------------------------------------------------------|
| <span data-ttu-id="44652-299">**Name**</span><span class="sxs-lookup"><span data-stu-id="44652-299">**Name**</span></span>       | <span data-ttu-id="44652-300">是</span><span class="sxs-lookup"><span data-stu-id="44652-300">Yes</span></span>         | <span data-ttu-id="44652-301">实体容器的名称。</span><span class="sxs-lookup"><span data-stu-id="44652-301">The name of the entity container.</span></span> <span data-ttu-id="44652-302">此名称不能包含句点 (.)。</span><span class="sxs-lookup"><span data-stu-id="44652-302">This name cannot contain periods (.).</span></span> |

> [!NOTE]
> <span data-ttu-id="44652-303">可以将任意数量的批注特性（自定义 XML 特性）应用于**EntityContainer**元素。</span><span class="sxs-lookup"><span data-stu-id="44652-303">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityContainer** element.</span></span> <span data-ttu-id="44652-304">然而，自定义特性可能不属于为 SSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="44652-304">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="44652-305">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="44652-305">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="44652-306">示例</span><span class="sxs-lookup"><span data-stu-id="44652-306">Example</span></span>

<span data-ttu-id="44652-307">下面的示例演示一个**EntityContainer**元素，该元素定义了两个实体集和一个关联集。</span><span class="sxs-lookup"><span data-stu-id="44652-307">The following example shows an **EntityContainer** element that defines two entity sets and one association set.</span></span> <span data-ttu-id="44652-308">注意，实体类型和关联类型名称由概念模型命名空间名称限定。</span><span class="sxs-lookup"><span data-stu-id="44652-308">Note that entity type and association type names are qualified by the conceptual model namespace name.</span></span>

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

## <a name="entityset-element-ssdl"></a><span data-ttu-id="44652-309">EntitySet 元素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="44652-309">EntitySet Element (SSDL)</span></span>

<span data-ttu-id="44652-310">存储架构定义语言（SSDL）中的**EntitySet**元素表示基础数据库中的表或视图。</span><span class="sxs-lookup"><span data-stu-id="44652-310">An **EntitySet** element in store schema definition language (SSDL) represents a table or view in the underlying database.</span></span> <span data-ttu-id="44652-311">以 SSDL 表示的 EntityType 元素表示表或视图中的行。</span><span class="sxs-lookup"><span data-stu-id="44652-311">An EntityType element in SSDL represents a row in the table or view.</span></span> <span data-ttu-id="44652-312">**EntitySet**元素的**EntityType**特性指定了表示 ssdl 实体集中的行的特定 SSDL 实体类型。</span><span class="sxs-lookup"><span data-stu-id="44652-312">The **EntityType** attribute of an **EntitySet** element specifies the particular SSDL entity type that represents rows in an SSDL entity set.</span></span> <span data-ttu-id="44652-313">CSDL 实体集和 SSDL 实体集之间的映射在 EntitySetMapping 元素中指定。</span><span class="sxs-lookup"><span data-stu-id="44652-313">The mapping between a CSDL entity set and an SSDL entity set is specified in an EntitySetMapping element.</span></span>

<span data-ttu-id="44652-314">**EntitySet**元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="44652-314">The **EntitySet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="44652-315">文档（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="44652-315">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="44652-316">DefiningQuery （零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="44652-316">DefiningQuery (zero or one element)</span></span>
-   <span data-ttu-id="44652-317">批注元素</span><span class="sxs-lookup"><span data-stu-id="44652-317">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="44652-318">适用的特性</span><span class="sxs-lookup"><span data-stu-id="44652-318">Applicable Attributes</span></span>

<span data-ttu-id="44652-319">下表介绍可应用于**EntitySet**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="44652-319">The following table describes the attributes that can be applied to the **EntitySet** element.</span></span>

> [!NOTE]
> <span data-ttu-id="44652-320">某些属性（此处未列出）可以用**存储**别名进行限定。</span><span class="sxs-lookup"><span data-stu-id="44652-320">Some attributes (not listed here) may be qualified with the **store** alias.</span></span> <span data-ttu-id="44652-321">在更新模型时，模型更新向导会使用这些特性。</span><span class="sxs-lookup"><span data-stu-id="44652-321">These attributes are used by the Update Model Wizard when updating a model.</span></span>

| <span data-ttu-id="44652-322">特性名</span><span class="sxs-lookup"><span data-stu-id="44652-322">Attribute Name</span></span> | <span data-ttu-id="44652-323">是否必需</span><span class="sxs-lookup"><span data-stu-id="44652-323">Is Required</span></span> | <span data-ttu-id="44652-324">值</span><span class="sxs-lookup"><span data-stu-id="44652-324">Value</span></span>                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| <span data-ttu-id="44652-325">**Name**</span><span class="sxs-lookup"><span data-stu-id="44652-325">**Name**</span></span>       | <span data-ttu-id="44652-326">是</span><span class="sxs-lookup"><span data-stu-id="44652-326">Yes</span></span>         | <span data-ttu-id="44652-327">实体集的名称。</span><span class="sxs-lookup"><span data-stu-id="44652-327">The name of the entity set.</span></span>                                                              |
| <span data-ttu-id="44652-328">**EntityType**</span><span class="sxs-lookup"><span data-stu-id="44652-328">**EntityType**</span></span> | <span data-ttu-id="44652-329">是</span><span class="sxs-lookup"><span data-stu-id="44652-329">Yes</span></span>         | <span data-ttu-id="44652-330">实体集包含其实例的实体类型的完全限定名称。</span><span class="sxs-lookup"><span data-stu-id="44652-330">The fully-qualified name of the entity type for which the entity set contains instances.</span></span> |
| <span data-ttu-id="44652-331">**架构**</span><span class="sxs-lookup"><span data-stu-id="44652-331">**Schema**</span></span>     | <span data-ttu-id="44652-332">是</span><span class="sxs-lookup"><span data-stu-id="44652-332">No</span></span>          | <span data-ttu-id="44652-333">数据库架构。</span><span class="sxs-lookup"><span data-stu-id="44652-333">The database schema.</span></span>                                                                     |
| <span data-ttu-id="44652-334">**Table**</span><span class="sxs-lookup"><span data-stu-id="44652-334">**Table**</span></span>      | <span data-ttu-id="44652-335">是</span><span class="sxs-lookup"><span data-stu-id="44652-335">No</span></span>          | <span data-ttu-id="44652-336">数据库表。</span><span class="sxs-lookup"><span data-stu-id="44652-336">The database table.</span></span>                                                                      |

> [!NOTE]
> <span data-ttu-id="44652-337">可以向**EntitySet**元素应用任意数量的批注特性（自定义 XML 特性）。</span><span class="sxs-lookup"><span data-stu-id="44652-337">Any number of annotation attributes (custom XML attributes) may be applied to the **EntitySet** element.</span></span> <span data-ttu-id="44652-338">然而，自定义特性可能不属于为 SSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="44652-338">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="44652-339">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="44652-339">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="44652-340">示例</span><span class="sxs-lookup"><span data-stu-id="44652-340">Example</span></span>

<span data-ttu-id="44652-341">下面的示例演示一个具有两个**EntitySet**元素和一个**AssociationSet**元素的**EntityContainer**元素：</span><span class="sxs-lookup"><span data-stu-id="44652-341">The following example shows an **EntityContainer** element that has two **EntitySet** elements and one **AssociationSet** element:</span></span>

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

## <a name="entitytype-element-ssdl"></a><span data-ttu-id="44652-342">EntityType 元素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="44652-342">EntityType Element (SSDL)</span></span>

<span data-ttu-id="44652-343">存储架构定义语言（SSDL）中的**EntityType**元素表示基础数据库的表或视图中的行。</span><span class="sxs-lookup"><span data-stu-id="44652-343">An **EntityType** element in store schema definition language (SSDL) represents a row in a table or view of the underlying database.</span></span> <span data-ttu-id="44652-344">以 SSDL 表示的 EntitySet 元素表示行所在的表或视图。</span><span class="sxs-lookup"><span data-stu-id="44652-344">An EntitySet element in SSDL represents the table or view in which rows occur.</span></span> <span data-ttu-id="44652-345">**EntitySet**元素的**EntityType**特性指定了表示 ssdl 实体集中的行的特定 SSDL 实体类型。</span><span class="sxs-lookup"><span data-stu-id="44652-345">The **EntityType** attribute of an **EntitySet** element specifies the particular SSDL entity type that represents rows in an SSDL entity set.</span></span> <span data-ttu-id="44652-346">SSDL 实体类型与 CSDL 实体类型之间的映射在 EntityTypeMapping 元素中指定。</span><span class="sxs-lookup"><span data-stu-id="44652-346">The mapping between an SSDL entity type and a CSDL entity type is specified in an EntityTypeMapping element.</span></span>

<span data-ttu-id="44652-347">**EntityType**元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="44652-347">The **EntityType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="44652-348">文档（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="44652-348">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="44652-349">Key（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="44652-349">Key (zero or one element)</span></span>
-   <span data-ttu-id="44652-350">批注元素</span><span class="sxs-lookup"><span data-stu-id="44652-350">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="44652-351">适用的特性</span><span class="sxs-lookup"><span data-stu-id="44652-351">Applicable Attributes</span></span>

<span data-ttu-id="44652-352">下表介绍可应用于**EntityType**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="44652-352">The table below describes the attributes that can be applied to the **EntityType** element.</span></span>

| <span data-ttu-id="44652-353">特性名</span><span class="sxs-lookup"><span data-stu-id="44652-353">Attribute Name</span></span> | <span data-ttu-id="44652-354">是否必需</span><span class="sxs-lookup"><span data-stu-id="44652-354">Is Required</span></span> | <span data-ttu-id="44652-355">值</span><span class="sxs-lookup"><span data-stu-id="44652-355">Value</span></span>                                                                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="44652-356">**Name**</span><span class="sxs-lookup"><span data-stu-id="44652-356">**Name**</span></span>       | <span data-ttu-id="44652-357">是</span><span class="sxs-lookup"><span data-stu-id="44652-357">Yes</span></span>         | <span data-ttu-id="44652-358">实体类型的名称。</span><span class="sxs-lookup"><span data-stu-id="44652-358">The name of the entity type.</span></span> <span data-ttu-id="44652-359">此值通常与以实体类型表示行的表的名称相同。</span><span class="sxs-lookup"><span data-stu-id="44652-359">This value is usually the same as the name of the table in which the entity type represents a row.</span></span> <span data-ttu-id="44652-360">此值可以不包含句点 (.)。</span><span class="sxs-lookup"><span data-stu-id="44652-360">This value can contain no periods (.).</span></span> |

> [!NOTE]
> <span data-ttu-id="44652-361">可以向**EntityType**元素应用任意数量的批注特性（自定义 XML 特性）。</span><span class="sxs-lookup"><span data-stu-id="44652-361">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityType** element.</span></span> <span data-ttu-id="44652-362">然而，自定义特性可能不属于为 SSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="44652-362">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="44652-363">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="44652-363">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="44652-364">示例</span><span class="sxs-lookup"><span data-stu-id="44652-364">Example</span></span>

<span data-ttu-id="44652-365">下面的示例演示具有两个属性的**EntityType**元素：</span><span class="sxs-lookup"><span data-stu-id="44652-365">The following example shows an **EntityType** element with two properties:</span></span>

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

## <a name="function-element-ssdl"></a><span data-ttu-id="44652-366">Function 元素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="44652-366">Function Element (SSDL)</span></span>

<span data-ttu-id="44652-367">存储架构定义语言（SSDL）中的**Function**元素指定基础数据库中存在的存储过程。</span><span class="sxs-lookup"><span data-stu-id="44652-367">The **Function** element in store schema definition language (SSDL) specifies a stored procedure that exists in the underlying database.</span></span>

<span data-ttu-id="44652-368">**函数**元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="44652-368">The **Function** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="44652-369">文档（零个或一个）</span><span class="sxs-lookup"><span data-stu-id="44652-369">Documentation (zero or one)</span></span>
-   <span data-ttu-id="44652-370">参数（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="44652-370">Parameter (zero or more)</span></span>
-   <span data-ttu-id="44652-371">CommandText （0个或1个）</span><span class="sxs-lookup"><span data-stu-id="44652-371">CommandText (zero or one)</span></span>
-   <span data-ttu-id="44652-372">ReturnType （零个或多个）</span><span class="sxs-lookup"><span data-stu-id="44652-372">ReturnType (zero or more)</span></span>
-   <span data-ttu-id="44652-373">批注元素（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="44652-373">Annotation elements (zero or more)</span></span>

<span data-ttu-id="44652-374">函数的返回类型必须与**returntype**元素或**returntype**属性（见下文）一起指定，但不能同时指定两者。</span><span class="sxs-lookup"><span data-stu-id="44652-374">A return type for a function must be specified with either the **ReturnType** element or the **ReturnType** attribute (see below), but not both.</span></span>

<span data-ttu-id="44652-375">可以将在存储模型中指定的存储过程导入应用程序的概念模型。</span><span class="sxs-lookup"><span data-stu-id="44652-375">Stored procedures that are specified in the storage model can be imported into the conceptual model of an application.</span></span> <span data-ttu-id="44652-376">有关详细信息，请参阅[用存储过程查询](~/ef6/modeling/designer/stored-procedures/query.md)。</span><span class="sxs-lookup"><span data-stu-id="44652-376">For more information, see [Querying with Stored Procedures](~/ef6/modeling/designer/stored-procedures/query.md).</span></span> <span data-ttu-id="44652-377">**函数**元素还可用于在存储模型中定义自定义函数。</span><span class="sxs-lookup"><span data-stu-id="44652-377">The **Function** element can also be used to define custom functions in the storage model.</span></span>  

### <a name="applicable-attributes"></a><span data-ttu-id="44652-378">适用的特性</span><span class="sxs-lookup"><span data-stu-id="44652-378">Applicable Attributes</span></span>

<span data-ttu-id="44652-379">下表介绍可应用于**Function**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="44652-379">The following table describes the attributes that can be applied to the **Function** element.</span></span>

> [!NOTE]
> <span data-ttu-id="44652-380">某些属性（此处未列出）可以用**存储**别名进行限定。</span><span class="sxs-lookup"><span data-stu-id="44652-380">Some attributes (not listed here) may be qualified with the **store** alias.</span></span> <span data-ttu-id="44652-381">在更新模型时，模型更新向导会使用这些特性。</span><span class="sxs-lookup"><span data-stu-id="44652-381">These attributes are used by the Update Model Wizard when updating a model.</span></span>

| <span data-ttu-id="44652-382">特性名</span><span class="sxs-lookup"><span data-stu-id="44652-382">Attribute Name</span></span>             | <span data-ttu-id="44652-383">是否必需</span><span class="sxs-lookup"><span data-stu-id="44652-383">Is Required</span></span> | <span data-ttu-id="44652-384">值</span><span class="sxs-lookup"><span data-stu-id="44652-384">Value</span></span>                                                                                                                                                                                                              |
|:---------------------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="44652-385">**Name**</span><span class="sxs-lookup"><span data-stu-id="44652-385">**Name**</span></span>                   | <span data-ttu-id="44652-386">是</span><span class="sxs-lookup"><span data-stu-id="44652-386">Yes</span></span>         | <span data-ttu-id="44652-387">存储过程的名称。</span><span class="sxs-lookup"><span data-stu-id="44652-387">The name of the stored procedure.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="44652-388">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="44652-388">**ReturnType**</span></span>             | <span data-ttu-id="44652-389">是</span><span class="sxs-lookup"><span data-stu-id="44652-389">No</span></span>          | <span data-ttu-id="44652-390">存储过程的返回类型。</span><span class="sxs-lookup"><span data-stu-id="44652-390">The return type of the stored procedure.</span></span>                                                                                                                                                                           |
| <span data-ttu-id="44652-391">**Aggregate**</span><span class="sxs-lookup"><span data-stu-id="44652-391">**Aggregate**</span></span>              | <span data-ttu-id="44652-392">是</span><span class="sxs-lookup"><span data-stu-id="44652-392">No</span></span>          | <span data-ttu-id="44652-393">如果存储过程返回聚合值，**则为 True** ;否则**为 False**。</span><span class="sxs-lookup"><span data-stu-id="44652-393">**True** if the stored procedure returns an aggregate value; otherwise **False**.</span></span>                                                                                                                                  |
| <span data-ttu-id="44652-394">**L**</span><span class="sxs-lookup"><span data-stu-id="44652-394">**BuiltIn**</span></span>                | <span data-ttu-id="44652-395">是</span><span class="sxs-lookup"><span data-stu-id="44652-395">No</span></span>          | <span data-ttu-id="44652-396">如果函数是内置的<sup>1</sup>函数，则为**True** ; 否则为。否则**为 False**。</span><span class="sxs-lookup"><span data-stu-id="44652-396">**True** if the function is a built-in<sup>1</sup> function; otherwise **False**.</span></span>                                                                                                                                  |
| <span data-ttu-id="44652-397">**StoreFunctionName**</span><span class="sxs-lookup"><span data-stu-id="44652-397">**StoreFunctionName**</span></span>      | <span data-ttu-id="44652-398">是</span><span class="sxs-lookup"><span data-stu-id="44652-398">No</span></span>          | <span data-ttu-id="44652-399">存储过程的名称。</span><span class="sxs-lookup"><span data-stu-id="44652-399">The name of the stored procedure.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="44652-400">**NiladicFunction**</span><span class="sxs-lookup"><span data-stu-id="44652-400">**NiladicFunction**</span></span>        | <span data-ttu-id="44652-401">是</span><span class="sxs-lookup"><span data-stu-id="44652-401">No</span></span>          | <span data-ttu-id="44652-402">如果函数是 niladic<sup>2</sup>函数，则为**True** ;否则**为 False** 。</span><span class="sxs-lookup"><span data-stu-id="44652-402">**True** if the function is a niladic<sup>2</sup> function; **False** otherwise.</span></span>                                                                                                                                   |
| <span data-ttu-id="44652-403">**IsComposable**</span><span class="sxs-lookup"><span data-stu-id="44652-403">**IsComposable**</span></span>           | <span data-ttu-id="44652-404">是</span><span class="sxs-lookup"><span data-stu-id="44652-404">No</span></span>          | <span data-ttu-id="44652-405">如果函数是可组合的<sup>3</sup>函数，则为**True** ;否则**为 False** 。</span><span class="sxs-lookup"><span data-stu-id="44652-405">**True** if the function is a composable<sup>3</sup> function; **False** otherwise.</span></span>                                                                                                                                |
| <span data-ttu-id="44652-406">**ParameterTypeSemantics**</span><span class="sxs-lookup"><span data-stu-id="44652-406">**ParameterTypeSemantics**</span></span> | <span data-ttu-id="44652-407">是</span><span class="sxs-lookup"><span data-stu-id="44652-407">No</span></span>          | <span data-ttu-id="44652-408">定义用于解析函数重载的类型语义的枚举。</span><span class="sxs-lookup"><span data-stu-id="44652-408">The enumeration that defines the type semantics used to resolve function overloads.</span></span> <span data-ttu-id="44652-409">该枚举是在提供程序清单中根据函数定义来定义的。</span><span class="sxs-lookup"><span data-stu-id="44652-409">The enumeration is defined in the provider manifest per function definition.</span></span> <span data-ttu-id="44652-410">默认值为**AllowImplicitConversion**。</span><span class="sxs-lookup"><span data-stu-id="44652-410">The default value is **AllowImplicitConversion**.</span></span> |
| <span data-ttu-id="44652-411">**架构**</span><span class="sxs-lookup"><span data-stu-id="44652-411">**Schema**</span></span>                 | <span data-ttu-id="44652-412">是</span><span class="sxs-lookup"><span data-stu-id="44652-412">No</span></span>          | <span data-ttu-id="44652-413">在其中定义存储过程的架构的名称。</span><span class="sxs-lookup"><span data-stu-id="44652-413">The name of the schema in which the stored procedure is defined.</span></span>                                                                                                                                                   |

<span data-ttu-id="44652-414"><sup>1</sup>内置函数是在数据库中定义的函数。</span><span class="sxs-lookup"><span data-stu-id="44652-414"><sup>1</sup> A built-in function is a function that is defined in the database.</span></span> <span data-ttu-id="44652-415">有关在存储模型中定义的函数的信息，请参阅 CommandText 元素（SSDL）。</span><span class="sxs-lookup"><span data-stu-id="44652-415">For information about functions that are defined in the storage model, see CommandText Element (SSDL).</span></span>

<span data-ttu-id="44652-416"><sup>2</sup> niladic 函数是不接受任何参数的函数，并且在调用时不需要括号。</span><span class="sxs-lookup"><span data-stu-id="44652-416"><sup>2</sup> A niladic function is a function that accepts no parameters and, when called, does not require parentheses.</span></span>

<span data-ttu-id="44652-417"><sup>3</sup>如果一个函数的输出可以是另一个函数的输入，则这两个函数是可组合的。</span><span class="sxs-lookup"><span data-stu-id="44652-417"><sup>3</sup> Two functions are composable if the output of one function can be the input for the other function.</span></span>

> [!NOTE]
> <span data-ttu-id="44652-418">可以将任意数量的批注特性（自定义 XML 特性）应用于**Function**元素。</span><span class="sxs-lookup"><span data-stu-id="44652-418">Any number of annotation attributes (custom XML attributes) may be applied to the **Function** element.</span></span> <span data-ttu-id="44652-419">然而，自定义特性可能不属于为 SSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="44652-419">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="44652-420">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="44652-420">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="44652-421">示例</span><span class="sxs-lookup"><span data-stu-id="44652-421">Example</span></span>

<span data-ttu-id="44652-422">下面的示例显示了与**UpdateOrderQuantity**存储过程对应的**Function**元素。</span><span class="sxs-lookup"><span data-stu-id="44652-422">The following example shows a **Function** element that corresponds to the **UpdateOrderQuantity** stored procedure.</span></span> <span data-ttu-id="44652-423">该存储过程接受两个参数，且不返回值。</span><span class="sxs-lookup"><span data-stu-id="44652-423">The stored procedure accepts two parameters and does not return a value.</span></span>

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

## <a name="key-element-ssdl"></a><span data-ttu-id="44652-424">Key 元素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="44652-424">Key Element (SSDL)</span></span>

<span data-ttu-id="44652-425">以存储架构定义语言（SSDL）表示的**Key**元素表示基础数据库中的表的主键。</span><span class="sxs-lookup"><span data-stu-id="44652-425">The **Key** element in store schema definition language (SSDL) represents the primary key of a table in the underlying database.</span></span> <span data-ttu-id="44652-426">**Key**是 EntityType 元素的子元素，它表示表中的行。</span><span class="sxs-lookup"><span data-stu-id="44652-426">**Key** is a child element of an EntityType element, which represents a row in a table.</span></span> <span data-ttu-id="44652-427">通过引用**EntityType**元素中定义的一个或多个属性元素，在**key**元素中定义主键。</span><span class="sxs-lookup"><span data-stu-id="44652-427">The primary key is defined in the **Key** element by referencing one or more Property elements that are defined on the **EntityType** element.</span></span>

<span data-ttu-id="44652-428">**Key**元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="44652-428">The **Key** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="44652-429">PropertyRef（一个或多个）</span><span class="sxs-lookup"><span data-stu-id="44652-429">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="44652-430">批注元素</span><span class="sxs-lookup"><span data-stu-id="44652-430">Annotation elements</span></span>

<span data-ttu-id="44652-431">没有任何特性适用于**该键**元素。</span><span class="sxs-lookup"><span data-stu-id="44652-431">No attributes are applicable to the **Key** element.</span></span>

### <a name="example"></a><span data-ttu-id="44652-432">示例</span><span class="sxs-lookup"><span data-stu-id="44652-432">Example</span></span>

<span data-ttu-id="44652-433">下面的示例演示一个具有引用一个属性的键的**EntityType**元素：</span><span class="sxs-lookup"><span data-stu-id="44652-433">The following example shows an **EntityType** element with a key that references one property:</span></span>

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

## <a name="ondelete-element-ssdl"></a><span data-ttu-id="44652-434">OnDelete 元素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="44652-434">OnDelete Element (SSDL)</span></span>

<span data-ttu-id="44652-435">存储架构定义语言（SSDL）中的**OnDelete**元素反映在删除参与 foreign key 约束的行时的数据库行为。</span><span class="sxs-lookup"><span data-stu-id="44652-435">The **OnDelete** element in store schema definition language (SSDL) reflects the database behavior when a row that participates in a foreign key constraint is deleted.</span></span> <span data-ttu-id="44652-436">如果将操作设置为**Cascade**，则还将删除引用要删除的行的行。</span><span class="sxs-lookup"><span data-stu-id="44652-436">If the action is set to **Cascade**, then rows that reference a row that is being deleted will also be deleted.</span></span> <span data-ttu-id="44652-437">如果将操作设置为 "**无**"，则不会同时删除引用要删除行的行。</span><span class="sxs-lookup"><span data-stu-id="44652-437">If the action is set to **None**, then rows that reference a row that is being deleted are not also deleted.</span></span> <span data-ttu-id="44652-438">**OnDelete**元素是结束元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="44652-438">An **OnDelete** element is a child element of an End element.</span></span>

<span data-ttu-id="44652-439">**OnDelete**元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="44652-439">An **OnDelete** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="44652-440">文档（零个或一个）</span><span class="sxs-lookup"><span data-stu-id="44652-440">Documentation (zero or one)</span></span>
-   <span data-ttu-id="44652-441">批注元素（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="44652-441">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="44652-442">适用的特性</span><span class="sxs-lookup"><span data-stu-id="44652-442">Applicable Attributes</span></span>

<span data-ttu-id="44652-443">下表介绍可应用于**OnDelete**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="44652-443">The following table describes the attributes that can be applied to the **OnDelete** element.</span></span>

| <span data-ttu-id="44652-444">特性名</span><span class="sxs-lookup"><span data-stu-id="44652-444">Attribute Name</span></span> | <span data-ttu-id="44652-445">是否必需</span><span class="sxs-lookup"><span data-stu-id="44652-445">Is Required</span></span> | <span data-ttu-id="44652-446">值</span><span class="sxs-lookup"><span data-stu-id="44652-446">Value</span></span>                                                                                               |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------|
| <span data-ttu-id="44652-447">**Action**</span><span class="sxs-lookup"><span data-stu-id="44652-447">**Action**</span></span>     | <span data-ttu-id="44652-448">是</span><span class="sxs-lookup"><span data-stu-id="44652-448">Yes</span></span>         | <span data-ttu-id="44652-449">**Cascade**或**None**。</span><span class="sxs-lookup"><span data-stu-id="44652-449">**Cascade** or **None**.</span></span> <span data-ttu-id="44652-450">（**限制**值有效，但行为与**None**相同。）</span><span class="sxs-lookup"><span data-stu-id="44652-450">(The value **Restricted** is valid but has the same behavior as **None**.)</span></span> |

> [!NOTE]
> <span data-ttu-id="44652-451">可以将任意数量的批注特性（自定义 XML 特性）应用于**OnDelete**元素。</span><span class="sxs-lookup"><span data-stu-id="44652-451">Any number of annotation attributes (custom XML attributes) may be applied to the **OnDelete** element.</span></span> <span data-ttu-id="44652-452">然而，自定义特性可能不属于为 SSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="44652-452">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="44652-453">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="44652-453">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="44652-454">示例</span><span class="sxs-lookup"><span data-stu-id="44652-454">Example</span></span>

<span data-ttu-id="44652-455">下面的示例演示了一个**Association**元素，该元素定义了**FK\_CustomerOrders**外键约束。</span><span class="sxs-lookup"><span data-stu-id="44652-455">The following example shows an **Association** element that defines the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="44652-456">**OnDelete**元素指示在删除**customers**表中的某一行时，该**订单**表中引用**该特定**行的所有行都将被删除。</span><span class="sxs-lookup"><span data-stu-id="44652-456">The **OnDelete** element indicates that all rows in the **Orders** table that reference a particular row in the **Customers** table will be deleted if the row in the **Customers** table is deleted.</span></span>

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

## <a name="parameter-element-ssdl"></a><span data-ttu-id="44652-457">Parameter 元素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="44652-457">Parameter Element (SSDL)</span></span>

<span data-ttu-id="44652-458">存储架构定义语言（SSDL）中的**Parameter**元素是 Function 元素的子元素，它指定数据库中存储过程的参数。</span><span class="sxs-lookup"><span data-stu-id="44652-458">The **Parameter** element in store schema definition language (SSDL) is a child of the Function element that specifies parameters for a stored procedure in the database.</span></span>

<span data-ttu-id="44652-459">**Parameter**元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="44652-459">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="44652-460">文档（零个或一个）</span><span class="sxs-lookup"><span data-stu-id="44652-460">Documentation (zero or one)</span></span>
-   <span data-ttu-id="44652-461">批注元素（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="44652-461">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="44652-462">适用的特性</span><span class="sxs-lookup"><span data-stu-id="44652-462">Applicable Attributes</span></span>

<span data-ttu-id="44652-463">下表描述了可应用于**参数**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="44652-463">The table below describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="44652-464">特性名</span><span class="sxs-lookup"><span data-stu-id="44652-464">Attribute Name</span></span> | <span data-ttu-id="44652-465">是否必需</span><span class="sxs-lookup"><span data-stu-id="44652-465">Is Required</span></span> | <span data-ttu-id="44652-466">值</span><span class="sxs-lookup"><span data-stu-id="44652-466">Value</span></span>                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="44652-467">**Name**</span><span class="sxs-lookup"><span data-stu-id="44652-467">**Name**</span></span>       | <span data-ttu-id="44652-468">是</span><span class="sxs-lookup"><span data-stu-id="44652-468">Yes</span></span>         | <span data-ttu-id="44652-469">参数的名称。</span><span class="sxs-lookup"><span data-stu-id="44652-469">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="44652-470">类型</span><span class="sxs-lookup"><span data-stu-id="44652-470">**Type**</span></span>       | <span data-ttu-id="44652-471">是</span><span class="sxs-lookup"><span data-stu-id="44652-471">Yes</span></span>         | <span data-ttu-id="44652-472">参数类型。</span><span class="sxs-lookup"><span data-stu-id="44652-472">The parameter type.</span></span>                                                                                                                                                                                                             |
| <span data-ttu-id="44652-473">**众**</span><span class="sxs-lookup"><span data-stu-id="44652-473">**Mode**</span></span>       | <span data-ttu-id="44652-474">是</span><span class="sxs-lookup"><span data-stu-id="44652-474">No</span></span>          | <span data-ttu-id="44652-475">**In**、 **Out**或**InOut** ，具体取决于参数是输入、输出还是输入/输出参数。</span><span class="sxs-lookup"><span data-stu-id="44652-475">**In**, **Out**, or **InOut** depending on whether the parameter is an input, output, or input/output parameter.</span></span>                                                                                                                |
| <span data-ttu-id="44652-476">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="44652-476">**MaxLength**</span></span>  | <span data-ttu-id="44652-477">是</span><span class="sxs-lookup"><span data-stu-id="44652-477">No</span></span>          | <span data-ttu-id="44652-478">参数的最大长度。</span><span class="sxs-lookup"><span data-stu-id="44652-478">The maximum length of the parameter.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="44652-479">**精度**</span><span class="sxs-lookup"><span data-stu-id="44652-479">**Precision**</span></span>  | <span data-ttu-id="44652-480">是</span><span class="sxs-lookup"><span data-stu-id="44652-480">No</span></span>          | <span data-ttu-id="44652-481">参数的精度。</span><span class="sxs-lookup"><span data-stu-id="44652-481">The precision of the parameter.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="44652-482">缩放</span><span class="sxs-lookup"><span data-stu-id="44652-482">**Scale**</span></span>      | <span data-ttu-id="44652-483">是</span><span class="sxs-lookup"><span data-stu-id="44652-483">No</span></span>          | <span data-ttu-id="44652-484">参数的小数位数。</span><span class="sxs-lookup"><span data-stu-id="44652-484">The scale of the parameter.</span></span>                                                                                                                                                                                                     |
| <span data-ttu-id="44652-485">**SRID**</span><span class="sxs-lookup"><span data-stu-id="44652-485">**SRID**</span></span>       | <span data-ttu-id="44652-486">是</span><span class="sxs-lookup"><span data-stu-id="44652-486">No</span></span>          | <span data-ttu-id="44652-487">空间系统引用标识符。</span><span class="sxs-lookup"><span data-stu-id="44652-487">Spatial System Reference Identifier.</span></span> <span data-ttu-id="44652-488">仅对空间类型的参数有效。</span><span class="sxs-lookup"><span data-stu-id="44652-488">Valid only for parameters of spatial types.</span></span> <span data-ttu-id="44652-489">有关详细信息，请参阅[SRID](https://en.wikipedia.org/wiki/SRID) and [SRID （SQL Server）](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="44652-489">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |

> [!NOTE]
> <span data-ttu-id="44652-490">可以将任意数量的批注特性（自定义 XML 特性）应用于**参数**元素。</span><span class="sxs-lookup"><span data-stu-id="44652-490">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="44652-491">然而，自定义特性可能不属于为 SSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="44652-491">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="44652-492">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="44652-492">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="44652-493">示例</span><span class="sxs-lookup"><span data-stu-id="44652-493">Example</span></span>

<span data-ttu-id="44652-494">下面的示例演示一个具有两个参数元素的**函数**元素，这些**参数**元素指定输入参数：</span><span class="sxs-lookup"><span data-stu-id="44652-494">The following example shows a **Function** element that has two **Parameter** elements that specify input parameters:</span></span>

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

## <a name="principal-element-ssdl"></a><span data-ttu-id="44652-495">Principal 元素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="44652-495">Principal Element (SSDL)</span></span>

<span data-ttu-id="44652-496">以存储架构定义语言（SSDL）表示的**主体**元素是 ReferentialConstraint 元素的子元素，用于定义外键约束（也称为 "引用约束"）的主体端。</span><span class="sxs-lookup"><span data-stu-id="44652-496">The **Principal** element in store schema definition language (SSDL) is a child element to the ReferentialConstraint element that defines the principal end of a foreign key constraint (also called a referential constraint).</span></span> <span data-ttu-id="44652-497">**主体**元素指定表中一个或多个列（或列）引用的主键列。</span><span class="sxs-lookup"><span data-stu-id="44652-497">The **Principal** element specifies the primary key column (or columns) in a table that is referenced by another column (or columns).</span></span> <span data-ttu-id="44652-498">**PropertyRef**元素指定要引用的列。</span><span class="sxs-lookup"><span data-stu-id="44652-498">**PropertyRef** elements specify which columns are referenced.</span></span> <span data-ttu-id="44652-499">Dependent 元素指定引用**主体**元素中指定的主键列的列。</span><span class="sxs-lookup"><span data-stu-id="44652-499">The Dependent element specifies columns that reference the primary key columns that are specified in the **Principal** element.</span></span>

<span data-ttu-id="44652-500">**主体**元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="44652-500">The **Principal** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="44652-501">PropertyRef（一个或多个）</span><span class="sxs-lookup"><span data-stu-id="44652-501">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="44652-502">批注元素（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="44652-502">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="44652-503">适用的特性</span><span class="sxs-lookup"><span data-stu-id="44652-503">Applicable Attributes</span></span>

<span data-ttu-id="44652-504">下表介绍可应用于**主体**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="44652-504">The following table describes the attributes that can be applied to the **Principal** element.</span></span>

| <span data-ttu-id="44652-505">特性名</span><span class="sxs-lookup"><span data-stu-id="44652-505">Attribute Name</span></span> | <span data-ttu-id="44652-506">是否必需</span><span class="sxs-lookup"><span data-stu-id="44652-506">Is Required</span></span> | <span data-ttu-id="44652-507">值</span><span class="sxs-lookup"><span data-stu-id="44652-507">Value</span></span>                                                                                                                                                      |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="44652-508">**角色**</span><span class="sxs-lookup"><span data-stu-id="44652-508">**Role**</span></span>       | <span data-ttu-id="44652-509">是</span><span class="sxs-lookup"><span data-stu-id="44652-509">Yes</span></span>         | <span data-ttu-id="44652-510">与相应的 End 元素的**Role**特性（如果使用）相同的值;否则，为包含被引用列的表的名称。</span><span class="sxs-lookup"><span data-stu-id="44652-510">The same value as the **Role** attribute (if used) of the corresponding End element; otherwise, the name of the table that contains the referenced column.</span></span> |

> [!NOTE]
> <span data-ttu-id="44652-511">可以将任意数量的批注特性（自定义 XML 特性）应用于**主体**元素。</span><span class="sxs-lookup"><span data-stu-id="44652-511">Any number of annotation attributes (custom XML attributes) may be applied to the **Principal** element.</span></span> <span data-ttu-id="44652-512">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="44652-512">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="44652-513">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="44652-513">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="44652-514">示例</span><span class="sxs-lookup"><span data-stu-id="44652-514">Example</span></span>

<span data-ttu-id="44652-515">下面的示例演示一个 Association 元素，该元素使用**ReferentialConstraint**元素指定参与**FK\_CustomerOrders**外键约束的列。</span><span class="sxs-lookup"><span data-stu-id="44652-515">The following example shows an Association element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="44652-516">**主体**元素将**Customer**表的**CustomerId**列指定为约束的主体端。</span><span class="sxs-lookup"><span data-stu-id="44652-516">The **Principal** element specifies the **CustomerId** column of the **Customer** table as the principal end of the constraint.</span></span>

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

## <a name="property-element-ssdl"></a><span data-ttu-id="44652-517">Property 元素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="44652-517">Property Element (SSDL)</span></span>

<span data-ttu-id="44652-518">存储架构定义语言（SSDL）中的**属性**元素表示基础数据库中的表的列。</span><span class="sxs-lookup"><span data-stu-id="44652-518">The **Property** element in store schema definition language (SSDL) represents a column in a table in the underlying database.</span></span> <span data-ttu-id="44652-519">**属性**元素是 EntityType 元素的子元素，表示表中的行。</span><span class="sxs-lookup"><span data-stu-id="44652-519">**Property** elements are children of EntityType elements, which represent rows in a table.</span></span> <span data-ttu-id="44652-520">**EntityType**元素上定义的每个**属性**元素都表示一列。</span><span class="sxs-lookup"><span data-stu-id="44652-520">Each **Property** element defined on an **EntityType** element represents a column.</span></span>

<span data-ttu-id="44652-521">**属性**元素不能具有任何子元素。</span><span class="sxs-lookup"><span data-stu-id="44652-521">A **Property** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="44652-522">适用的特性</span><span class="sxs-lookup"><span data-stu-id="44652-522">Applicable Attributes</span></span>

<span data-ttu-id="44652-523">下表介绍可应用于**属性**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="44652-523">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="44652-524">特性名</span><span class="sxs-lookup"><span data-stu-id="44652-524">Attribute Name</span></span>            | <span data-ttu-id="44652-525">是否必需</span><span class="sxs-lookup"><span data-stu-id="44652-525">Is Required</span></span> | <span data-ttu-id="44652-526">值</span><span class="sxs-lookup"><span data-stu-id="44652-526">Value</span></span>                                                                                                                                                                                                                           |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="44652-527">**Name**</span><span class="sxs-lookup"><span data-stu-id="44652-527">**Name**</span></span>                  | <span data-ttu-id="44652-528">是</span><span class="sxs-lookup"><span data-stu-id="44652-528">Yes</span></span>         | <span data-ttu-id="44652-529">对应列的名称。</span><span class="sxs-lookup"><span data-stu-id="44652-529">The name of the corresponding column.</span></span>                                                                                                                                                                                           |
| <span data-ttu-id="44652-530">类型</span><span class="sxs-lookup"><span data-stu-id="44652-530">**Type**</span></span>                  | <span data-ttu-id="44652-531">是</span><span class="sxs-lookup"><span data-stu-id="44652-531">Yes</span></span>         | <span data-ttu-id="44652-532">对应列的类型。</span><span class="sxs-lookup"><span data-stu-id="44652-532">The type of the corresponding column.</span></span>                                                                                                                                                                                           |
| <span data-ttu-id="44652-533">**可以为 null**</span><span class="sxs-lookup"><span data-stu-id="44652-533">**Nullable**</span></span>              | <span data-ttu-id="44652-534">是</span><span class="sxs-lookup"><span data-stu-id="44652-534">No</span></span>          | <span data-ttu-id="44652-535">**True** （默认值）或**False** ，具体取决于对应列是否可以具有 null 值。</span><span class="sxs-lookup"><span data-stu-id="44652-535">**True** (the default value) or **False** depending on whether the corresponding column can have a null value.</span></span>                                                                                                                  |
| <span data-ttu-id="44652-536">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="44652-536">**DefaultValue**</span></span>          | <span data-ttu-id="44652-537">是</span><span class="sxs-lookup"><span data-stu-id="44652-537">No</span></span>          | <span data-ttu-id="44652-538">对应列的默认值。</span><span class="sxs-lookup"><span data-stu-id="44652-538">The default value of the corresponding column.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="44652-539">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="44652-539">**MaxLength**</span></span>             | <span data-ttu-id="44652-540">是</span><span class="sxs-lookup"><span data-stu-id="44652-540">No</span></span>          | <span data-ttu-id="44652-541">对应列的最大长度。</span><span class="sxs-lookup"><span data-stu-id="44652-541">The maximum length of the corresponding column.</span></span>                                                                                                                                                                                 |
| <span data-ttu-id="44652-542">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="44652-542">**FixedLength**</span></span>           | <span data-ttu-id="44652-543">是</span><span class="sxs-lookup"><span data-stu-id="44652-543">No</span></span>          | <span data-ttu-id="44652-544">**True**或**False** ，具体取决于相应的列值是否将作为固定长度字符串存储。</span><span class="sxs-lookup"><span data-stu-id="44652-544">**True** or **False** depending on whether the corresponding column value will be stored as a fixed length string.</span></span>                                                                                                              |
| <span data-ttu-id="44652-545">**精度**</span><span class="sxs-lookup"><span data-stu-id="44652-545">**Precision**</span></span>             | <span data-ttu-id="44652-546">是</span><span class="sxs-lookup"><span data-stu-id="44652-546">No</span></span>          | <span data-ttu-id="44652-547">对应列的精度。</span><span class="sxs-lookup"><span data-stu-id="44652-547">The precision of the corresponding column.</span></span>                                                                                                                                                                                      |
| <span data-ttu-id="44652-548">缩放</span><span class="sxs-lookup"><span data-stu-id="44652-548">**Scale**</span></span>                 | <span data-ttu-id="44652-549">是</span><span class="sxs-lookup"><span data-stu-id="44652-549">No</span></span>          | <span data-ttu-id="44652-550">对应列的小数位数。</span><span class="sxs-lookup"><span data-stu-id="44652-550">The scale of the corresponding column.</span></span>                                                                                                                                                                                          |
| <span data-ttu-id="44652-551">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="44652-551">**Unicode**</span></span>               | <span data-ttu-id="44652-552">是</span><span class="sxs-lookup"><span data-stu-id="44652-552">No</span></span>          | <span data-ttu-id="44652-553">**True**或**False** ，具体取决于对应列值是否将存储为 Unicode 字符串。</span><span class="sxs-lookup"><span data-stu-id="44652-553">**True** or **False** depending on whether the corresponding column value will be stored as a Unicode string.</span></span>                                                                                                                   |
| <span data-ttu-id="44652-554">**归类**</span><span class="sxs-lookup"><span data-stu-id="44652-554">**Collation**</span></span>             | <span data-ttu-id="44652-555">是</span><span class="sxs-lookup"><span data-stu-id="44652-555">No</span></span>          | <span data-ttu-id="44652-556">指定要在数据源中使用的排序序列的字符串。</span><span class="sxs-lookup"><span data-stu-id="44652-556">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |
| <span data-ttu-id="44652-557">**SRID**</span><span class="sxs-lookup"><span data-stu-id="44652-557">**SRID**</span></span>                  | <span data-ttu-id="44652-558">是</span><span class="sxs-lookup"><span data-stu-id="44652-558">No</span></span>          | <span data-ttu-id="44652-559">空间系统引用标识符。</span><span class="sxs-lookup"><span data-stu-id="44652-559">Spatial System Reference Identifier.</span></span> <span data-ttu-id="44652-560">仅对空间类型的属性有效。</span><span class="sxs-lookup"><span data-stu-id="44652-560">Valid only for properties of spatial types.</span></span> <span data-ttu-id="44652-561">有关详细信息，请参阅[SRID](https://en.wikipedia.org/wiki/SRID) and [SRID （SQL Server）](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="44652-561">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="44652-562">**StoreGeneratedPattern**</span><span class="sxs-lookup"><span data-stu-id="44652-562">**StoreGeneratedPattern**</span></span> | <span data-ttu-id="44652-563">是</span><span class="sxs-lookup"><span data-stu-id="44652-563">No</span></span>          | <span data-ttu-id="44652-564">**无**、**标识**（如果相应的列值是在数据库中生成的标识）或**计算**（如果在数据库中计算了相应的列值）。</span><span class="sxs-lookup"><span data-stu-id="44652-564">**None**, **Identity** (if the corresponding column value is an identity that is generated in the database), or **Computed** (if the corresponding column value is computed in the database).</span></span> <span data-ttu-id="44652-565">对于 RowType 属性无效。</span><span class="sxs-lookup"><span data-stu-id="44652-565">Not Valid for RowType properties.</span></span> |

> [!NOTE]
> <span data-ttu-id="44652-566">可以将任意数量的批注特性（自定义 XML 特性）应用于**Property**元素。</span><span class="sxs-lookup"><span data-stu-id="44652-566">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="44652-567">然而，自定义特性可能不属于为 SSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="44652-567">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="44652-568">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="44652-568">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="44652-569">示例</span><span class="sxs-lookup"><span data-stu-id="44652-569">Example</span></span>

<span data-ttu-id="44652-570">下面的示例演示一个具有两个子**属性**元素的**EntityType**元素：</span><span class="sxs-lookup"><span data-stu-id="44652-570">The following example shows an **EntityType** element with two child **Property** elements:</span></span>

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

## <a name="propertyref-element-ssdl"></a><span data-ttu-id="44652-571">PropertyRef 元素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="44652-571">PropertyRef Element (SSDL)</span></span>

<span data-ttu-id="44652-572">存储架构定义语言（SSDL）中的**PropertyRef**元素引用 EntityType 元素上定义的属性，以指示该属性将执行下列角色之一：</span><span class="sxs-lookup"><span data-stu-id="44652-572">The **PropertyRef** element in store schema definition language (SSDL) references a property defined on an EntityType element to indicate that the property will perform one of the following roles:</span></span>

-   <span data-ttu-id="44652-573">是**EntityType**表示的表的主键的一部分。</span><span class="sxs-lookup"><span data-stu-id="44652-573">Be part of the primary key of the table that the **EntityType** represents.</span></span> <span data-ttu-id="44652-574">一个或多个**PropertyRef**元素可用于定义主键。</span><span class="sxs-lookup"><span data-stu-id="44652-574">One or more **PropertyRef** elements can be used to define a primary key.</span></span> <span data-ttu-id="44652-575">有关更多信息，请参见 Key 元素。</span><span class="sxs-lookup"><span data-stu-id="44652-575">For more information, see Key element.</span></span>
-   <span data-ttu-id="44652-576">是引用约束的依赖端或主体端。</span><span class="sxs-lookup"><span data-stu-id="44652-576">Be the dependent or principal end of a referential constraint.</span></span> <span data-ttu-id="44652-577">有关更多信息，请参见 ReferentialConstraint 元素。</span><span class="sxs-lookup"><span data-stu-id="44652-577">For more information, see ReferentialConstraint element.</span></span>

<span data-ttu-id="44652-578">**PropertyRef**元素只能具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="44652-578">The **PropertyRef** element can only have the following child elements:</span></span>

-   <span data-ttu-id="44652-579">文档（零个或一个）</span><span class="sxs-lookup"><span data-stu-id="44652-579">Documentation (zero or one)</span></span>
-   <span data-ttu-id="44652-580">批注元素</span><span class="sxs-lookup"><span data-stu-id="44652-580">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="44652-581">适用的特性</span><span class="sxs-lookup"><span data-stu-id="44652-581">Applicable Attributes</span></span>

<span data-ttu-id="44652-582">下表介绍可应用于**PropertyRef**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="44652-582">The table below describes the attributes that can be applied to the **PropertyRef** element.</span></span>

| <span data-ttu-id="44652-583">特性名</span><span class="sxs-lookup"><span data-stu-id="44652-583">Attribute Name</span></span> | <span data-ttu-id="44652-584">是否必需</span><span class="sxs-lookup"><span data-stu-id="44652-584">Is Required</span></span> | <span data-ttu-id="44652-585">值</span><span class="sxs-lookup"><span data-stu-id="44652-585">Value</span></span>                                |
|:---------------|:------------|:-------------------------------------|
| <span data-ttu-id="44652-586">**Name**</span><span class="sxs-lookup"><span data-stu-id="44652-586">**Name**</span></span>       | <span data-ttu-id="44652-587">是</span><span class="sxs-lookup"><span data-stu-id="44652-587">Yes</span></span>         | <span data-ttu-id="44652-588">所引用属性的名称。</span><span class="sxs-lookup"><span data-stu-id="44652-588">The name of the referenced property.</span></span> |

> [!NOTE]
> <span data-ttu-id="44652-589">可以将任意数量的批注特性（自定义 XML 特性）应用于**PropertyRef**元素。</span><span class="sxs-lookup"><span data-stu-id="44652-589">Any number of annotation attributes (custom XML attributes) may be applied to the **PropertyRef** element.</span></span> <span data-ttu-id="44652-590">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="44652-590">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="44652-591">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="44652-591">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="44652-592">示例</span><span class="sxs-lookup"><span data-stu-id="44652-592">Example</span></span>

<span data-ttu-id="44652-593">下面的示例演示了一个**PropertyRef**元素，该元素用于通过引用**EntityType**元素上定义的属性来定义主键。</span><span class="sxs-lookup"><span data-stu-id="44652-593">The following example shows a **PropertyRef** element used to define a primary key by referencing a property that is defined on an **EntityType** element.</span></span>

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

## <a name="referentialconstraint-element-ssdl"></a><span data-ttu-id="44652-594">ReferentialConstraint 元素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="44652-594">ReferentialConstraint Element (SSDL)</span></span>

<span data-ttu-id="44652-595">存储架构定义语言（SSDL）中的**ReferentialConstraint**元素表示基础数据库中的外键约束（也称为引用完整性约束）。</span><span class="sxs-lookup"><span data-stu-id="44652-595">The **ReferentialConstraint** element in store schema definition language (SSDL) represents a foreign key constraint (also called a referential integrity constraint) in the underlying database.</span></span> <span data-ttu-id="44652-596">约束的主体端和依赖端分别由 Principal 和 Dependent 子元素指定。</span><span class="sxs-lookup"><span data-stu-id="44652-596">The principal and dependent ends of the constraint are specified by the Principal and Dependent child elements, respectively.</span></span> <span data-ttu-id="44652-597">通过 PropertyRef 元素引用参与主体端和依赖端的列。</span><span class="sxs-lookup"><span data-stu-id="44652-597">Columns that participate in the principal and dependent ends are referenced with PropertyRef elements.</span></span>

<span data-ttu-id="44652-598">**ReferentialConstraint**元素是 Association 元素的可选子元素。</span><span class="sxs-lookup"><span data-stu-id="44652-598">The **ReferentialConstraint** element is an optional child element of the Association element.</span></span> <span data-ttu-id="44652-599">如果未使用**ReferentialConstraint**元素映射**Association**元素中指定的 foreign key 约束，则必须使用 AssociationSetMapping 元素来执行此操作。</span><span class="sxs-lookup"><span data-stu-id="44652-599">If a **ReferentialConstraint** element is not used to map the foreign key constraint that is specified in the **Association** element, an AssociationSetMapping element must be used to do this.</span></span>

<span data-ttu-id="44652-600">**ReferentialConstraint**元素可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="44652-600">The **ReferentialConstraint** element can have the following child elements:</span></span>

-   <span data-ttu-id="44652-601">文档（零个或一个）</span><span class="sxs-lookup"><span data-stu-id="44652-601">Documentation (zero or one)</span></span>
-   <span data-ttu-id="44652-602">Principal（恰好一个）</span><span class="sxs-lookup"><span data-stu-id="44652-602">Principal (exactly one)</span></span>
-   <span data-ttu-id="44652-603">依赖项（恰好一个）</span><span class="sxs-lookup"><span data-stu-id="44652-603">Dependent (exactly one)</span></span>
-   <span data-ttu-id="44652-604">批注元素（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="44652-604">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="44652-605">适用的特性</span><span class="sxs-lookup"><span data-stu-id="44652-605">Applicable Attributes</span></span>

<span data-ttu-id="44652-606">可以将任意数量的批注特性（自定义 XML 特性）应用于**ReferentialConstraint**元素。</span><span class="sxs-lookup"><span data-stu-id="44652-606">Any number of annotation attributes (custom XML attributes) may be applied to the **ReferentialConstraint** element.</span></span> <span data-ttu-id="44652-607">然而，自定义特性可能不属于为 SSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="44652-607">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="44652-608">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="44652-608">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="44652-609">示例</span><span class="sxs-lookup"><span data-stu-id="44652-609">Example</span></span>

<span data-ttu-id="44652-610">下面的示例演示一个**Association**元素，该元素使用**ReferentialConstraint**元素指定参与**FK\_CustomerOrders**外键约束的列：</span><span class="sxs-lookup"><span data-stu-id="44652-610">The following example shows an **Association** element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint:</span></span>

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

## <a name="returntype-element-ssdl"></a><span data-ttu-id="44652-611">ReturnType 元素（SSDL）</span><span class="sxs-lookup"><span data-stu-id="44652-611">ReturnType Element (SSDL)</span></span>

<span data-ttu-id="44652-612">存储架构定义语言（SSDL）中的**ReturnType**元素指定**函数**元素中定义的函数的返回类型。</span><span class="sxs-lookup"><span data-stu-id="44652-612">The **ReturnType** element in store schema definition language (SSDL) specifies the return type for a function that is defined in a **Function** element.</span></span> <span data-ttu-id="44652-613">还可以使用**ReturnType**特性指定函数返回类型。</span><span class="sxs-lookup"><span data-stu-id="44652-613">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="44652-614">函数的返回类型是使用**type**特性或**ReturnType**元素指定的。</span><span class="sxs-lookup"><span data-stu-id="44652-614">The return type of a function is specified with the **Type** attribute or the **ReturnType** element.</span></span>

<span data-ttu-id="44652-615">**ReturnType**元素可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="44652-615">The **ReturnType** element can have the following child elements:</span></span>

- <span data-ttu-id="44652-616">CollectionType （一）</span><span class="sxs-lookup"><span data-stu-id="44652-616">CollectionType (one)</span></span>  

> [!NOTE]
> <span data-ttu-id="44652-617">可以向**ReturnType**元素应用任意数量的批注特性（自定义 XML 特性）。</span><span class="sxs-lookup"><span data-stu-id="44652-617">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** element.</span></span> <span data-ttu-id="44652-618">然而，自定义特性可能不属于为 SSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="44652-618">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="44652-619">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="44652-619">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="44652-620">示例</span><span class="sxs-lookup"><span data-stu-id="44652-620">Example</span></span>

<span data-ttu-id="44652-621">下面的示例使用一个**函数**，该函数返回行的集合。</span><span class="sxs-lookup"><span data-stu-id="44652-621">The following example uses a **Function** that returns a collection of rows.</span></span>

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


## <a name="rowtype-element-ssdl"></a><span data-ttu-id="44652-622">RowType 元素（SSDL）</span><span class="sxs-lookup"><span data-stu-id="44652-622">RowType Element (SSDL)</span></span>

<span data-ttu-id="44652-623">存储架构定义语言（SSDL）中的**RowType**元素将未命名的结构定义为在存储区中定义的函数的返回类型。</span><span class="sxs-lookup"><span data-stu-id="44652-623">A **RowType** element in store schema definition language (SSDL) defines an unnamed structure as a return type for a function defined in the store.</span></span>

<span data-ttu-id="44652-624">**RowType**元素是**CollectionType**元素的子元素：</span><span class="sxs-lookup"><span data-stu-id="44652-624">A **RowType** element is the child element of **CollectionType** element:</span></span>

<span data-ttu-id="44652-625">**RowType**元素可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="44652-625">A **RowType** element can have the following child elements:</span></span>

- <span data-ttu-id="44652-626">Property（一个或多个）</span><span class="sxs-lookup"><span data-stu-id="44652-626">Property (one or more)</span></span>  

### <a name="example"></a><span data-ttu-id="44652-627">示例</span><span class="sxs-lookup"><span data-stu-id="44652-627">Example</span></span>

<span data-ttu-id="44652-628">下面的示例演示一个存储函数，该函数使用**CollectionType**元素来指定该函数返回行的集合（在**RowType**元素中指定）。</span><span class="sxs-lookup"><span data-stu-id="44652-628">The following example shows a store function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>


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

## <a name="schema-element-ssdl"></a><span data-ttu-id="44652-629">Schema 元素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="44652-629">Schema Element (SSDL)</span></span>

<span data-ttu-id="44652-630">存储架构定义语言（SSDL）中的**架构**元素是存储模型定义的根元素。</span><span class="sxs-lookup"><span data-stu-id="44652-630">The **Schema** element in store schema definition language (SSDL) is the root element of a storage model definition.</span></span> <span data-ttu-id="44652-631">它包括构成存储模型的对象、函数和容器的定义。</span><span class="sxs-lookup"><span data-stu-id="44652-631">It contains definitions for the objects, functions, and containers that make up a storage model.</span></span>

<span data-ttu-id="44652-632">**Schema**元素可包含零个或多个下列子元素：</span><span class="sxs-lookup"><span data-stu-id="44652-632">The **Schema** element may contain zero or more of the following child elements:</span></span>

-   <span data-ttu-id="44652-633">关联</span><span class="sxs-lookup"><span data-stu-id="44652-633">Association</span></span>
-   <span data-ttu-id="44652-634">EntityType</span><span class="sxs-lookup"><span data-stu-id="44652-634">EntityType</span></span>
-   <span data-ttu-id="44652-635">EntityContainer</span><span class="sxs-lookup"><span data-stu-id="44652-635">EntityContainer</span></span>
-   <span data-ttu-id="44652-636">函数</span><span class="sxs-lookup"><span data-stu-id="44652-636">Function</span></span>

<span data-ttu-id="44652-637">**Schema**元素使用**namespace**属性为存储模型中的实体类型和关联对象定义命名空间。</span><span class="sxs-lookup"><span data-stu-id="44652-637">The **Schema** element uses the **Namespace** attribute to define the namespace for the entity type and association objects in a storage model.</span></span> <span data-ttu-id="44652-638">在命名空间内，任何两个对象都不能同名。</span><span class="sxs-lookup"><span data-stu-id="44652-638">Within a namespace, no two objects can have the same name.</span></span>

<span data-ttu-id="44652-639">存储模型命名空间不同于**Schema**元素的 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="44652-639">A storage model namespace is different from the XML namespace of the **Schema** element.</span></span> <span data-ttu-id="44652-640">存储模型命名空间（由**命名空间**特性定义）是实体类型和关联类型的逻辑容器。</span><span class="sxs-lookup"><span data-stu-id="44652-640">A storage model namespace (as defined by the **Namespace** attribute) is a logical container for entity types and association types.</span></span> <span data-ttu-id="44652-641">**Schema**元素的 XML 命名空间（由**xmlns**特性指示）是**schema**元素的子元素和属性的默认命名空间。</span><span class="sxs-lookup"><span data-stu-id="44652-641">The XML namespace (indicated by the **xmlns** attribute) of a **Schema** element is the default namespace for child elements and attributes of the **Schema** element.</span></span> <span data-ttu-id="44652-642">窗体的 XML 命名空间 https://schemas.microsoft.com/ado/YYYY/MM/edm/ssdl （其中 YYYY 和 MM 表示年和月分别） 是为 SSDL 保留。</span><span class="sxs-lookup"><span data-stu-id="44652-642">XML namespaces of the form https://schemas.microsoft.com/ado/YYYY/MM/edm/ssdl (where YYYY and MM represent a year and month respectively) are reserved for SSDL.</span></span> <span data-ttu-id="44652-643">自定义元素和特性不能位于具有此格式的命名空间中。</span><span class="sxs-lookup"><span data-stu-id="44652-643">Custom elements and attributes cannot be in namespaces that have this form.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="44652-644">适用的特性</span><span class="sxs-lookup"><span data-stu-id="44652-644">Applicable Attributes</span></span>

<span data-ttu-id="44652-645">下表描述了可对**Schema**元素应用的属性。</span><span class="sxs-lookup"><span data-stu-id="44652-645">The table below describes the attributes can be applied to the **Schema** element.</span></span>

| <span data-ttu-id="44652-646">特性名</span><span class="sxs-lookup"><span data-stu-id="44652-646">Attribute Name</span></span>            | <span data-ttu-id="44652-647">是否必需</span><span class="sxs-lookup"><span data-stu-id="44652-647">Is Required</span></span> | <span data-ttu-id="44652-648">值</span><span class="sxs-lookup"><span data-stu-id="44652-648">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="44652-649">**Namespace**</span><span class="sxs-lookup"><span data-stu-id="44652-649">**Namespace**</span></span>             | <span data-ttu-id="44652-650">是</span><span class="sxs-lookup"><span data-stu-id="44652-650">Yes</span></span>         | <span data-ttu-id="44652-651">存储模型的命名空间。</span><span class="sxs-lookup"><span data-stu-id="44652-651">The namespace of the storage model.</span></span> <span data-ttu-id="44652-652">**命名空间**属性的值用于构成类型的完全限定名称。</span><span class="sxs-lookup"><span data-stu-id="44652-652">The value of the **Namespace** attribute is used to form the fully qualified name of a type.</span></span> <span data-ttu-id="44652-653">例如，如果名为*Customer*的**entitytype**在位于 examplemodel.store 命名空间中，则**entitytype**的完全限定名称为位于 examplemodel.store。</span><span class="sxs-lookup"><span data-stu-id="44652-653">For example, if an **EntityType** named *Customer* is in the ExampleModel.Store namespace, then the fully qualified name of the **EntityType** is ExampleModel.Store.Customer.</span></span> <br/> <span data-ttu-id="44652-654">以下字符串不能用作**Namespace**特性的值： **System**、**暂时性**或**Edm**。</span><span class="sxs-lookup"><span data-stu-id="44652-654">The following strings cannot be used as the value for the **Namespace** attribute: **System**, **Transient**, or **Edm**.</span></span> <span data-ttu-id="44652-655">**Namespace**属性的值不能与 CSDL Schema 元素中**namespace**属性的值相同。</span><span class="sxs-lookup"><span data-stu-id="44652-655">The value for the **Namespace** attribute cannot be the same as the value for the **Namespace** attribute in the CSDL Schema element.</span></span> |
| <span data-ttu-id="44652-656">**Alias**</span><span class="sxs-lookup"><span data-stu-id="44652-656">**Alias**</span></span>                 | <span data-ttu-id="44652-657">是</span><span class="sxs-lookup"><span data-stu-id="44652-657">No</span></span>          | <span data-ttu-id="44652-658">用于取代命名空间名称的标识符。</span><span class="sxs-lookup"><span data-stu-id="44652-658">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="44652-659">例如，如果名为*Customer*的**EntityType**位于位于 examplemodel.store 命名空间中，并且**Alias**属性的值为*Storagemodel.customer*，则可以使用 storagemodel.customer 作为 EntityType 的完全限定名称 **。**</span><span class="sxs-lookup"><span data-stu-id="44652-659">For example, if an **EntityType** named *Customer* is in the ExampleModel.Store namespace and the value of the **Alias** attribute is *StorageModel*, then you can use StorageModel.Customer as the fully qualified name of the **EntityType.**</span></span>                                                                                                                                                                                                                                                                                    |
| <span data-ttu-id="44652-660">**提供程序**</span><span class="sxs-lookup"><span data-stu-id="44652-660">**Provider**</span></span>              | <span data-ttu-id="44652-661">是</span><span class="sxs-lookup"><span data-stu-id="44652-661">Yes</span></span>         | <span data-ttu-id="44652-662">数据提供程序。</span><span class="sxs-lookup"><span data-stu-id="44652-662">The data provider.</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| <span data-ttu-id="44652-663">**ProviderManifestToken**</span><span class="sxs-lookup"><span data-stu-id="44652-663">**ProviderManifestToken**</span></span> | <span data-ttu-id="44652-664">是</span><span class="sxs-lookup"><span data-stu-id="44652-664">Yes</span></span>         | <span data-ttu-id="44652-665">一个标记，该标记指示提供程序清单返回到的提供程序。</span><span class="sxs-lookup"><span data-stu-id="44652-665">A token that indicates to the provider which provider manifest to return.</span></span> <span data-ttu-id="44652-666">没有为该标记定义格式。</span><span class="sxs-lookup"><span data-stu-id="44652-666">No format for the token is defined.</span></span> <span data-ttu-id="44652-667">标记的值由提供程序定义。</span><span class="sxs-lookup"><span data-stu-id="44652-667">Values for the token are defined by the provider.</span></span> <span data-ttu-id="44652-668">有关 SQL Server 提供程序清单令牌的信息，请参阅 SqlClient for 实体框架。</span><span class="sxs-lookup"><span data-stu-id="44652-668">For information about SQL Server provider manifest tokens, see SqlClient for Entity Framework.</span></span>                                                                                                                                                                                                                                                                                                                        |

### <a name="example"></a><span data-ttu-id="44652-669">示例</span><span class="sxs-lookup"><span data-stu-id="44652-669">Example</span></span>

<span data-ttu-id="44652-670">下面的示例演示一个**架构**元素，该元素包含一个**EntityContainer**元素、两个**EntityType**元素和一个**Association**元素。</span><span class="sxs-lookup"><span data-stu-id="44652-670">The following example shows a **Schema** element that contains an **EntityContainer** element, two **EntityType** elements, and one **Association** element.</span></span>

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

## <a name="annotation-attributes"></a><span data-ttu-id="44652-671">Annotation 特性</span><span class="sxs-lookup"><span data-stu-id="44652-671">Annotation Attributes</span></span>

<span data-ttu-id="44652-672">以存储架构定义语言 (SSDL) 表示的批注特性在存储模型中是自定义 XML 特性，这些特性提供有关存储模型中元素的额外元数据。</span><span class="sxs-lookup"><span data-stu-id="44652-672">Annotation attributes in store schema definition language (SSDL) are custom XML attributes in the storage model that provide extra metadata about the elements in the storage model.</span></span> <span data-ttu-id="44652-673">除了具有有效的 XML 结构之外，以下约束也适用于批注特性：</span><span class="sxs-lookup"><span data-stu-id="44652-673">In addition to having valid XML structure, the following constraints apply to annotation attributes:</span></span>

-   <span data-ttu-id="44652-674">批注特性不能位于为 SSDL 保留的任何 XML 命名空间中。</span><span class="sxs-lookup"><span data-stu-id="44652-674">Annotation attributes must not be in any XML namespace that is reserved for SSDL.</span></span>
-   <span data-ttu-id="44652-675">任何两个批注特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="44652-675">The fully-qualified names of any two annotation attributes must not be the same.</span></span>

<span data-ttu-id="44652-676">可以将多个批注特性应用于一个给定的 SSDL 元素。</span><span class="sxs-lookup"><span data-stu-id="44652-676">More than one annotation attribute may be applied to a given SSDL element.</span></span> <span data-ttu-id="44652-677">可以在运行时通过使用 System.web 命名空间中的类访问批注元素中包含的元数据。</span><span class="sxs-lookup"><span data-stu-id="44652-677">Metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="44652-678">示例</span><span class="sxs-lookup"><span data-stu-id="44652-678">Example</span></span>

<span data-ttu-id="44652-679">下面的示例演示一个 EntityType 元素，该元素具有应用于 "**订单 id** " 属性的批注特性。</span><span class="sxs-lookup"><span data-stu-id="44652-679">The following example shows an EntityType element that has an annotation attribute applied to the **OrderId** property.</span></span> <span data-ttu-id="44652-680">该示例还显示了添加到**EntityType**元素的批注元素。</span><span class="sxs-lookup"><span data-stu-id="44652-680">The example also show an annotation element added to the **EntityType** element.</span></span>

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

## <a name="annotation-elements-ssdl"></a><span data-ttu-id="44652-681">批注元素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="44652-681">Annotation Elements (SSDL)</span></span>

<span data-ttu-id="44652-682">以存储架构定义语言 (SSDL) 表示的批注元素是存储模型中的自定义 XML 元素，可提供有关存储模型的额外元数据。</span><span class="sxs-lookup"><span data-stu-id="44652-682">Annotation elements in store schema definition language (SSDL) are custom XML elements in the storage model that provide extra metadata about the storage model.</span></span> <span data-ttu-id="44652-683">除了具有有效的 XML 结构之外，批注元素还应满足以下约束：</span><span class="sxs-lookup"><span data-stu-id="44652-683">In addition to having valid XML structure, the following constraints apply to annotation elements:</span></span>

-   <span data-ttu-id="44652-684">批注元素不能位于为 SSDL 保留的任何 XML 命名空间中。</span><span class="sxs-lookup"><span data-stu-id="44652-684">Annotation elements must not be in any XML namespace that is reserved for SSDL.</span></span>
-   <span data-ttu-id="44652-685">任何两个批注元素的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="44652-685">The fully-qualified names of any two annotation elements must not be the same.</span></span>
-   <span data-ttu-id="44652-686">批注元素必须出现在给定 SSDL 元素的所有其他子元素之后。</span><span class="sxs-lookup"><span data-stu-id="44652-686">Annotation elements must appear after all other child elements of a given SSDL element.</span></span>

<span data-ttu-id="44652-687">多个批注元素可能是某个给定 SSDL 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="44652-687">More than one annotation element may be a child of a given SSDL element.</span></span> <span data-ttu-id="44652-688">从 .NET Framework 版本4开始，可以在运行时通过使用 System.web 命名空间中的类访问批注元素中包含的元数据。</span><span class="sxs-lookup"><span data-stu-id="44652-688">Starting with the .NET Framework version 4, metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="44652-689">示例</span><span class="sxs-lookup"><span data-stu-id="44652-689">Example</span></span>

<span data-ttu-id="44652-690">下面的示例显示具有 annotation 元素（**CustomElement**）的 EntityType 元素。</span><span class="sxs-lookup"><span data-stu-id="44652-690">The following example shows an EntityType element that has an annotation element (**CustomElement**).</span></span> <span data-ttu-id="44652-691">该示例还显示了应用于 "**订单 id** " 属性的批注特性。</span><span class="sxs-lookup"><span data-stu-id="44652-691">The example also shows an annotation attribute applied to the **OrderId** property.</span></span>

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

## <a name="facets-ssdl"></a><span data-ttu-id="44652-692">方面 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="44652-692">Facets (SSDL)</span></span>

<span data-ttu-id="44652-693">以存储架构定义语言 (SSDL) 表示的方面表示对于 Property 元素中指定的列类型的约束。</span><span class="sxs-lookup"><span data-stu-id="44652-693">Facets in store schema definition language (SSDL) represent constraints on column types that are specified in Property elements.</span></span> <span data-ttu-id="44652-694">Facet 作为**属性**元素上的 XML 特性实现。</span><span class="sxs-lookup"><span data-stu-id="44652-694">Facets are implemented as XML attributes on **Property** elements.</span></span>

<span data-ttu-id="44652-695">下表描述了 SSDL 中支持的方面：</span><span class="sxs-lookup"><span data-stu-id="44652-695">The following table describes the facets that are supported in SSDL:</span></span>

| <span data-ttu-id="44652-696">方面</span><span class="sxs-lookup"><span data-stu-id="44652-696">Facet</span></span>           | <span data-ttu-id="44652-697">说明</span><span class="sxs-lookup"><span data-stu-id="44652-697">Description</span></span>                                                                                                                                                                                                                                                 |
|:----------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="44652-698">**归类**</span><span class="sxs-lookup"><span data-stu-id="44652-698">**Collation**</span></span>   | <span data-ttu-id="44652-699">指定在对属性值执行比较和排序操作时要使用的排序序列。</span><span class="sxs-lookup"><span data-stu-id="44652-699">Specifies the collating sequence (or sorting sequence) to be used when performing comparison and ordering operations on values of the property.</span></span>                                                                                                             |
| <span data-ttu-id="44652-700">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="44652-700">**FixedLength**</span></span> | <span data-ttu-id="44652-701">指定列值的长度是否可变。</span><span class="sxs-lookup"><span data-stu-id="44652-701">Specifies whether the length of the column value can vary.</span></span>                                                                                                                                                                                                  |
| <span data-ttu-id="44652-702">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="44652-702">**MaxLength**</span></span>   | <span data-ttu-id="44652-703">指定列值的最大长度。</span><span class="sxs-lookup"><span data-stu-id="44652-703">Specifies the maximum length of the column value.</span></span>                                                                                                                                                                                                           |
| <span data-ttu-id="44652-704">**精度**</span><span class="sxs-lookup"><span data-stu-id="44652-704">**Precision**</span></span>   | <span data-ttu-id="44652-705">对于**Decimal**类型的属性，指定属性值可以具有的位数。</span><span class="sxs-lookup"><span data-stu-id="44652-705">For properties of type **Decimal**, specifies the number of digits a property value can have.</span></span> <span data-ttu-id="44652-706">对于类型为**Time**、 **DateTime**和**DateTimeOffset**的属性，指定列值的秒小数部分的位数。</span><span class="sxs-lookup"><span data-stu-id="44652-706">For properties of type **Time**, **DateTime**, and **DateTimeOffset**, specifies the number of digits for the fractional part of seconds of the column value.</span></span> |
| <span data-ttu-id="44652-707">缩放</span><span class="sxs-lookup"><span data-stu-id="44652-707">**Scale**</span></span>       | <span data-ttu-id="44652-708">指定列值小数点右侧的位数。</span><span class="sxs-lookup"><span data-stu-id="44652-708">Specifies the number of digits to the right of the decimal point for the column value.</span></span>                                                                                                                                                                      |
| <span data-ttu-id="44652-709">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="44652-709">**Unicode**</span></span>     | <span data-ttu-id="44652-710">指示是否将列值存储为 Unicode。</span><span class="sxs-lookup"><span data-stu-id="44652-710">Indicates whether the column value is stored as Unicode.</span></span>                                                                                                                                                                                                    |
