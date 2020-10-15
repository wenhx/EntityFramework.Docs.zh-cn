---
title: 实体框架术语表-EF6
description: 实体框架6术语表
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/resources/glossary
ms.openlocfilehash: 6c11b45978ad8efaf70bb640567c79b60ff7a708
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064648"
---
# <a name="entity-framework-glossary"></a><span data-ttu-id="18c9a-103">实体框架术语表</span><span class="sxs-lookup"><span data-stu-id="18c9a-103">Entity Framework Glossary</span></span>
## <a name="code-first"></a><span data-ttu-id="18c9a-104">Code First</span><span class="sxs-lookup"><span data-stu-id="18c9a-104">Code First</span></span>
<span data-ttu-id="18c9a-105">使用代码创建实体框架模型。</span><span class="sxs-lookup"><span data-stu-id="18c9a-105">Creating an Entity Framework model using code.</span></span> <span data-ttu-id="18c9a-106">该模型可以面向现有数据库或新数据库。</span><span class="sxs-lookup"><span data-stu-id="18c9a-106">The model can target an existing database or a new database.</span></span>

## <a name="context"></a><span data-ttu-id="18c9a-107">上下文</span><span class="sxs-lookup"><span data-stu-id="18c9a-107">Context</span></span>
<span data-ttu-id="18c9a-108">一个类，它表示与数据库的会话，使您能够查询和保存数据。</span><span class="sxs-lookup"><span data-stu-id="18c9a-108">A class that represents a session with the database, allowing you to query and save data.</span></span> <span data-ttu-id="18c9a-109">上下文派生自 DbContext 或 ObjectContext 类。</span><span class="sxs-lookup"><span data-stu-id="18c9a-109">A context derives from the DbContext or ObjectContext class.</span></span>

## <a name="convention-code-first"></a><span data-ttu-id="18c9a-110">约定 (Code First) </span><span class="sxs-lookup"><span data-stu-id="18c9a-110">Convention (Code First)</span></span>
<span data-ttu-id="18c9a-111">实体框架使用来从类推断模型形状的规则。</span><span class="sxs-lookup"><span data-stu-id="18c9a-111">A rule that Entity Framework uses to infer the shape of you model from your classes.</span></span>

## <a name="database-first"></a><span data-ttu-id="18c9a-112">Database First</span><span class="sxs-lookup"><span data-stu-id="18c9a-112">Database First</span></span>
<span data-ttu-id="18c9a-113">使用 EF 设计器创建面向现有数据库的实体框架模型。</span><span class="sxs-lookup"><span data-stu-id="18c9a-113">Creating an Entity Framework model, using the EF Designer, that targets an existing database.</span></span>

## <a name="eager-loading"></a><span data-ttu-id="18c9a-114">预先加载</span><span class="sxs-lookup"><span data-stu-id="18c9a-114">Eager loading</span></span>
<span data-ttu-id="18c9a-115">加载相关数据的一种模式，其中一种类型的实体的查询还会将相关实体作为查询的一部分进行加载。</span><span class="sxs-lookup"><span data-stu-id="18c9a-115">A pattern of loading related data where a query for one type of entity also loads related entities as part of the query.</span></span>

## <a name="ef-designer"></a><span data-ttu-id="18c9a-116">EF 设计器</span><span class="sxs-lookup"><span data-stu-id="18c9a-116">EF Designer</span></span>
<span data-ttu-id="18c9a-117">Visual Studio 中的可视化设计器，可用于使用框和线条创建实体框架模型。</span><span class="sxs-lookup"><span data-stu-id="18c9a-117">A visual designer in Visual Studio that allows you to create an Entity Framework model using boxes and lines.</span></span>

## <a name="entity"></a><span data-ttu-id="18c9a-118">实体</span><span class="sxs-lookup"><span data-stu-id="18c9a-118">Entity</span></span>
<span data-ttu-id="18c9a-119">表示客户、产品和订单等应用程序数据的类或对象。</span><span class="sxs-lookup"><span data-stu-id="18c9a-119">A class or object that represents application data such as customers, products, and orders.</span></span>

## <a name="entity-data-model"></a><span data-ttu-id="18c9a-120">实体数据模型</span><span class="sxs-lookup"><span data-stu-id="18c9a-120">Entity Data Model</span></span>
<span data-ttu-id="18c9a-121">描述实体和它们之间的关系的模型。</span><span class="sxs-lookup"><span data-stu-id="18c9a-121">A model that describes entities and the relationships between them.</span></span> <span data-ttu-id="18c9a-122">EF 使用 EDM 来描述开发人员计划的概念模型。</span><span class="sxs-lookup"><span data-stu-id="18c9a-122">EF uses EDM to describe the conceptual model against which the developer programs.</span></span> <span data-ttu-id="18c9a-123">EDM 在 Chen 引入的实体关系模型的基础上生成。</span><span class="sxs-lookup"><span data-stu-id="18c9a-123">EDM builds on the Entity Relationship model introduced by Dr. Peter Chen.</span></span> <span data-ttu-id="18c9a-124">EDM 最初是使用成为 Microsoft 的一系列开发人员和服务器技术的主要目标而开发的。</span><span class="sxs-lookup"><span data-stu-id="18c9a-124">The EDM was originally developed with the primary goal of becoming the common data model across a suite of developer and server technologies from Microsoft.</span></span> <span data-ttu-id="18c9a-125">EDM 还可作为 OData 协议的一部分。</span><span class="sxs-lookup"><span data-stu-id="18c9a-125">EDM is also used as part of the OData protocol.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="18c9a-126">显式加载</span><span class="sxs-lookup"><span data-stu-id="18c9a-126">Explicit loading</span></span>
<span data-ttu-id="18c9a-127">一种用于加载相关数据的模式，其中通过调用 API 加载相关的对象。</span><span class="sxs-lookup"><span data-stu-id="18c9a-127">A pattern of loading related data where related objects are loaded by calling an API.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="18c9a-128">Fluent API</span><span class="sxs-lookup"><span data-stu-id="18c9a-128">Fluent API</span></span>
<span data-ttu-id="18c9a-129">可用于配置 Code First 模型的 API。</span><span class="sxs-lookup"><span data-stu-id="18c9a-129">An API that can be used to configure a Code First model.</span></span>

## <a name="foreign-key-association"></a><span data-ttu-id="18c9a-130">外键关联</span><span class="sxs-lookup"><span data-stu-id="18c9a-130">Foreign key association</span></span>
<span data-ttu-id="18c9a-131">实体之间的关联，其中表示外键的属性包含在依赖实体的类中。</span><span class="sxs-lookup"><span data-stu-id="18c9a-131">An association between entities where a property that represents the foreign key is included in the class of the dependent entity.</span></span> <span data-ttu-id="18c9a-132">例如，Product 包含 "类别 Id" 属性。</span><span class="sxs-lookup"><span data-stu-id="18c9a-132">For example, Product contains a CategoryId property.</span></span>

## <a name="identifying-relationship"></a><span data-ttu-id="18c9a-133">标识关系</span><span class="sxs-lookup"><span data-stu-id="18c9a-133">Identifying relationship</span></span>
<span data-ttu-id="18c9a-134">一种关系，其中主体实体的主键是依赖实体的主键的一部分。</span><span class="sxs-lookup"><span data-stu-id="18c9a-134">A relationship where the primary key of the principal entity is part of the primary key of the dependent entity.</span></span> <span data-ttu-id="18c9a-135">在这种关系中，没有主体实体，依赖实体就不能存在。</span><span class="sxs-lookup"><span data-stu-id="18c9a-135">In this kind of relationship, the dependent entity cannot exist without the principal entity.</span></span>

## <a name="independent-association"></a><span data-ttu-id="18c9a-136">独立关联</span><span class="sxs-lookup"><span data-stu-id="18c9a-136">Independent association</span></span>
<span data-ttu-id="18c9a-137">实体之间的关联，其中不存在表示依赖实体的类中的外键的属性。</span><span class="sxs-lookup"><span data-stu-id="18c9a-137">An association between entities where there is no property representing the foreign key in the class of the dependent entity.</span></span> <span data-ttu-id="18c9a-138">例如，Product 类包含与 Category 的关系，但没有 "类别 Id" 属性。</span><span class="sxs-lookup"><span data-stu-id="18c9a-138">For example, a Product class contains a relationship to Category but no CategoryId property.</span></span> <span data-ttu-id="18c9a-139">实体框架跟踪关联的状态，这与两个关联端的实体状态无关。</span><span class="sxs-lookup"><span data-stu-id="18c9a-139">Entity Framework tracks the state of the association independently of the state of the entities at the two association ends.</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="18c9a-140">延迟加载</span><span class="sxs-lookup"><span data-stu-id="18c9a-140">Lazy loading</span></span>
<span data-ttu-id="18c9a-141">一种加载相关数据的模式，在访问导航属性时，会自动加载相关的对象。</span><span class="sxs-lookup"><span data-stu-id="18c9a-141">A pattern of loading related data where related objects are automatically loaded when a navigation property is accessed.</span></span>

## <a name="model-first"></a><span data-ttu-id="18c9a-142">Model First</span><span class="sxs-lookup"><span data-stu-id="18c9a-142">Model First</span></span>
<span data-ttu-id="18c9a-143">使用 EF 设计器创建实体框架模型，然后使用该设计器创建新的数据库。</span><span class="sxs-lookup"><span data-stu-id="18c9a-143">Creating an Entity Framework model, using the EF Designer, that is then used to create a new database.</span></span>

## <a name="navigation-property"></a><span data-ttu-id="18c9a-144">导航属性</span><span class="sxs-lookup"><span data-stu-id="18c9a-144">Navigation property</span></span>
<span data-ttu-id="18c9a-145">引用其他实体的实体的属性。</span><span class="sxs-lookup"><span data-stu-id="18c9a-145">A property of an entity that references another entity.</span></span> <span data-ttu-id="18c9a-146">例如，Product 包含类别导航属性，类别包含 Products 导航属性。</span><span class="sxs-lookup"><span data-stu-id="18c9a-146">For example, Product contains a Category navigation property and Category contains a Products navigation property.</span></span>

## <a name="poco"></a><span data-ttu-id="18c9a-147">POCO</span><span class="sxs-lookup"><span data-stu-id="18c9a-147">POCO</span></span>
<span data-ttu-id="18c9a-148">Plain-Old CLR 对象的首字母缩写。</span><span class="sxs-lookup"><span data-stu-id="18c9a-148">Acronym for Plain-Old CLR Object.</span></span> <span data-ttu-id="18c9a-149">不与任何框架具有依赖关系的简单用户类。</span><span class="sxs-lookup"><span data-stu-id="18c9a-149">A simple user class that has no dependencies with any framework.</span></span> <span data-ttu-id="18c9a-150">在 EF 的上下文中，不是派生自 EntityObject 的实体类实现任何接口，或携带 EF 中定义的任何属性。</span><span class="sxs-lookup"><span data-stu-id="18c9a-150">In the context of EF, an entity class that does not derive from EntityObject, implements any interfaces or carries any attributes defined in EF.</span></span> <span data-ttu-id="18c9a-151">与持久性框架分离的此类实体类也称为 "持久性未知"。</span><span class="sxs-lookup"><span data-stu-id="18c9a-151">Such entity classes that are decoupled from the persistence framework are also said to be "persistence ignorant".</span></span>  

## <a name="relationship-inverse"></a><span data-ttu-id="18c9a-152">逆关系</span><span class="sxs-lookup"><span data-stu-id="18c9a-152">Relationship inverse</span></span>
<span data-ttu-id="18c9a-153">关系的相反结束，例如 product。类别和类别。产品.</span><span class="sxs-lookup"><span data-stu-id="18c9a-153">The opposite end of a relationship, for example, product.Category and category.Product.</span></span>

## <a name="self-tracking-entity"></a><span data-ttu-id="18c9a-154">自跟踪实体</span><span class="sxs-lookup"><span data-stu-id="18c9a-154">Self-tracking entity</span></span>
<span data-ttu-id="18c9a-155">从代码生成模板生成的实体，可帮助进行 N 层开发。</span><span class="sxs-lookup"><span data-stu-id="18c9a-155">An entity built from a code generation template that helps with N-Tier development.</span></span>

## <a name="table-per-concrete-type-tpc"></a><span data-ttu-id="18c9a-156">每个具体的表类型 (TPC) </span><span class="sxs-lookup"><span data-stu-id="18c9a-156">Table-per-concrete type (TPC)</span></span>
<span data-ttu-id="18c9a-157">一种映射继承的方法，其中层次结构中的每个非抽象类型都映射到数据库中的单独表。</span><span class="sxs-lookup"><span data-stu-id="18c9a-157">A method of mapping the inheritance where each non-abstract type in the hierarchy is mapped to separate table in the database.</span></span>

## <a name="table-per-hierarchy-tph"></a><span data-ttu-id="18c9a-158">每个层次结构一个表 (TPH) </span><span class="sxs-lookup"><span data-stu-id="18c9a-158">Table-per-hierarchy (TPH)</span></span>
<span data-ttu-id="18c9a-159">一种映射继承的方法，其中，层次结构中的所有类型都映射到数据库中的同一表。</span><span class="sxs-lookup"><span data-stu-id="18c9a-159">A method of mapping the inheritance where all types in the hierarchy are mapped to the same table in the database.</span></span> <span data-ttu-id="18c9a-160">鉴别器列 (s) 用于标识与每行关联的类型。</span><span class="sxs-lookup"><span data-stu-id="18c9a-160">A discriminator column(s) is used to identify what type each row is associated with.</span></span>

## <a name="table-per-type-tpt"></a><span data-ttu-id="18c9a-161">每种类型一个表 (TPT) </span><span class="sxs-lookup"><span data-stu-id="18c9a-161">Table-per-type (TPT)</span></span>
<span data-ttu-id="18c9a-162">一种映射继承的方法，其中层次结构中所有类型的公共属性都映射到数据库中的同一个表，但每个类型独有的属性将映射到一个单独的表。</span><span class="sxs-lookup"><span data-stu-id="18c9a-162">A method of mapping the inheritance where the common properties of all types in the hierarchy are mapped to the same table in the database, but properties unique to each type are mapped to a separate table.</span></span>

## <a name="type-discovery"></a><span data-ttu-id="18c9a-163">类型发现</span><span class="sxs-lookup"><span data-stu-id="18c9a-163">Type discovery</span></span>
<span data-ttu-id="18c9a-164">标识应属于实体框架模型的类型的过程。</span><span class="sxs-lookup"><span data-stu-id="18c9a-164">The process of identifying the types that should be part of an Entity Framework model.</span></span>
