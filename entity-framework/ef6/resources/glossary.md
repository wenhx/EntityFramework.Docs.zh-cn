---
title: 实体框架术语表-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 3f05ffdd-49bc-499c-9732-4a368bf5d2d7
ms.openlocfilehash: 4e42e5870879524b814cecdc361e688d36f0180f
ms.sourcegitcommit: 6c4e06bc62d98442530e93a44725e38e59483d42
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58131391"
---
# <a name="entity-framework-glossary"></a><span data-ttu-id="c4724-102">实体框架术语表</span><span class="sxs-lookup"><span data-stu-id="c4724-102">Entity Framework Glossary</span></span>
## <a name="code-first"></a><span data-ttu-id="c4724-103">Code First</span><span class="sxs-lookup"><span data-stu-id="c4724-103">Code First</span></span>
<span data-ttu-id="c4724-104">创建使用代码的实体框架模型。</span><span class="sxs-lookup"><span data-stu-id="c4724-104">Creating an Entity Framework model using code.</span></span> <span data-ttu-id="c4724-105">该模型可以将现有数据库或新的数据库为目标。</span><span class="sxs-lookup"><span data-stu-id="c4724-105">The model can target an existing database or a new database.</span></span>

## <a name="context"></a><span data-ttu-id="c4724-106">上下文</span><span class="sxs-lookup"><span data-stu-id="c4724-106">Context</span></span>
<span data-ttu-id="c4724-107">一个类，表示与数据库中，您可以查询和保存数据的会话。</span><span class="sxs-lookup"><span data-stu-id="c4724-107">A class that represents a session with the database, allowing you to query and save data.</span></span> <span data-ttu-id="c4724-108">上下文派生自 DbContext 或 ObjectContext 类。</span><span class="sxs-lookup"><span data-stu-id="c4724-108">A context derives from the DbContext or ObjectContext class.</span></span>

## <a name="convention-code-first"></a><span data-ttu-id="c4724-109">约定 （代码优先）</span><span class="sxs-lookup"><span data-stu-id="c4724-109">Convention (Code First)</span></span>
<span data-ttu-id="c4724-110">实体框架使用推断的模型从您的类形状的规则。</span><span class="sxs-lookup"><span data-stu-id="c4724-110">A rule that Entity Framework uses to infer the shape of you model from your classes.</span></span>

## <a name="database-first"></a><span data-ttu-id="c4724-111">数据库优先</span><span class="sxs-lookup"><span data-stu-id="c4724-111">Database First</span></span>
<span data-ttu-id="c4724-112">创建实体框架模型，使用 EF 设计器中，面向现有数据库。</span><span class="sxs-lookup"><span data-stu-id="c4724-112">Creating an Entity Framework model, using the EF Designer, that targets an existing database.</span></span>

## <a name="eager-loading"></a><span data-ttu-id="c4724-113">预先加载</span><span class="sxs-lookup"><span data-stu-id="c4724-113">Eager loading</span></span>
<span data-ttu-id="c4724-114">加载相关的数据的一种类型的实体的查询在查询的一部分，并加载相关的实体的模式。</span><span class="sxs-lookup"><span data-stu-id="c4724-114">A pattern of loading related data where a query for one type of entity also loads related entities as part of the query.</span></span>

## <a name="ef-designer"></a><span data-ttu-id="c4724-115">EF 设计器</span><span class="sxs-lookup"><span data-stu-id="c4724-115">EF Designer</span></span>
<span data-ttu-id="c4724-116">可以创建使用框和行的实体框架模型的 Visual Studio 中可视化设计器。</span><span class="sxs-lookup"><span data-stu-id="c4724-116">A visual designer in Visual Studio that allows you to create an Entity Framework model using boxes and lines.</span></span>

## <a name="entity"></a><span data-ttu-id="c4724-117">实体</span><span class="sxs-lookup"><span data-stu-id="c4724-117">Entity</span></span>
<span data-ttu-id="c4724-118">表示客户、产品和订单等应用程序数据的类或对象。</span><span class="sxs-lookup"><span data-stu-id="c4724-118">A class or object that represents application data such as customers, products, and orders.</span></span>

## <a name="entity-data-model"></a><span data-ttu-id="c4724-119">实体数据模型</span><span class="sxs-lookup"><span data-stu-id="c4724-119">Entity Data Model</span></span>
<span data-ttu-id="c4724-120">描述实体和它们之间的关系模型。</span><span class="sxs-lookup"><span data-stu-id="c4724-120">A model that describes entities and the relationships between them.</span></span> <span data-ttu-id="c4724-121">EF 使用 EDM 来描述概念模型对其开发人员计划。</span><span class="sxs-lookup"><span data-stu-id="c4724-121">EF uses EDM to describe the conceptual model against which the developer programs.</span></span> <span data-ttu-id="c4724-122">引入的灾难恢复的实体关系模型上构建 EDM。Peter Chen。</span><span class="sxs-lookup"><span data-stu-id="c4724-122">EDM builds on the Entity Relationship model introduced by Dr. Peter Chen.</span></span> <span data-ttu-id="c4724-123">EDM 的最初开发的一系列来自 Microsoft 的开发人员和服务器技术成为通用数据模型的主要目标。</span><span class="sxs-lookup"><span data-stu-id="c4724-123">The EDM was originally developed with the primary goal of becoming the common data model across a suite of developer and server technologies from Microsoft.</span></span> <span data-ttu-id="c4724-124">EDM 也用作 OData 协议的一部分。</span><span class="sxs-lookup"><span data-stu-id="c4724-124">EDM is also used as part of the OData protocol.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="c4724-125">显式加载</span><span class="sxs-lookup"><span data-stu-id="c4724-125">Explicit loading</span></span>
<span data-ttu-id="c4724-126">加载相关的对象通过调用 API 的加载位置的相关的数据的模式。</span><span class="sxs-lookup"><span data-stu-id="c4724-126">A pattern of loading related data where related objects are loaded by calling an API.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="c4724-127">Fluent API</span><span class="sxs-lookup"><span data-stu-id="c4724-127">Fluent API</span></span>
<span data-ttu-id="c4724-128">可用于配置 Code First 模型 API。</span><span class="sxs-lookup"><span data-stu-id="c4724-128">An API that can be used to configure a Code First model.</span></span>

## <a name="foreign-key-association"></a><span data-ttu-id="c4724-129">外键关联</span><span class="sxs-lookup"><span data-stu-id="c4724-129">Foreign key association</span></span>
<span data-ttu-id="c4724-130">其中该属性表示外键包含依赖的实体的类中的实体之间的关联。</span><span class="sxs-lookup"><span data-stu-id="c4724-130">An association between entities where a property that represents the foreign key is included in the class of the dependent entity.</span></span> <span data-ttu-id="c4724-131">例如，产品包含 CategoryId 属性。</span><span class="sxs-lookup"><span data-stu-id="c4724-131">For example, Product contains a CategoryId property.</span></span>

## <a name="identifying-relationship"></a><span data-ttu-id="c4724-132">标识关系</span><span class="sxs-lookup"><span data-stu-id="c4724-132">Identifying relationship</span></span>
<span data-ttu-id="c4724-133">一种关系，其中主体实体的主键是依赖实体的主键的一部分。</span><span class="sxs-lookup"><span data-stu-id="c4724-133">A relationship where the primary key of the principal entity is part of the primary key of the dependent entity.</span></span> <span data-ttu-id="c4724-134">在这种关系中，没有主体实体，依赖实体就不能存在。</span><span class="sxs-lookup"><span data-stu-id="c4724-134">In this kind of relationship, the dependent entity cannot exist without the principal entity.</span></span>

## <a name="independent-association"></a><span data-ttu-id="c4724-135">独立关联</span><span class="sxs-lookup"><span data-stu-id="c4724-135">Independent association</span></span>
<span data-ttu-id="c4724-136">实体之间的关联没有表示依赖实体的类中的外键没有属性。</span><span class="sxs-lookup"><span data-stu-id="c4724-136">An association between entities where there is no property representing the foreign key in the class of the dependent entity.</span></span> <span data-ttu-id="c4724-137">例如，Product 类包含一个到类别，但没有 CategoryId 属性关系。</span><span class="sxs-lookup"><span data-stu-id="c4724-137">For example, a Product class contains a relationship to Category but no CategoryId property.</span></span> <span data-ttu-id="c4724-138">实体框架跟踪独立于位于两个关联端的实体的状态关联的状态。</span><span class="sxs-lookup"><span data-stu-id="c4724-138">Entity Framework tracks the state of the association independently of the state of the entities at the two association ends.</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="c4724-139">延迟加载</span><span class="sxs-lookup"><span data-stu-id="c4724-139">Lazy loading</span></span>
<span data-ttu-id="c4724-140">加载相关的数据的相关的对象时，自动加载访问导航属性的模式。</span><span class="sxs-lookup"><span data-stu-id="c4724-140">A pattern of loading related data where related objects are automatically loaded when a navigation property is accessed.</span></span>

## <a name="model-first"></a><span data-ttu-id="c4724-141">模型优先</span><span class="sxs-lookup"><span data-stu-id="c4724-141">Model First</span></span>
<span data-ttu-id="c4724-142">创建实体框架模型，使用 EF 设计器，然后用于创建新的数据库。</span><span class="sxs-lookup"><span data-stu-id="c4724-142">Creating an Entity Framework model, using the EF Designer, that is then used to create a new database.</span></span>

## <a name="navigation-property"></a><span data-ttu-id="c4724-143">导航属性</span><span class="sxs-lookup"><span data-stu-id="c4724-143">Navigation property</span></span>
<span data-ttu-id="c4724-144">实体引用另一个实体的属性。</span><span class="sxs-lookup"><span data-stu-id="c4724-144">A property of an entity that references another entity.</span></span> <span data-ttu-id="c4724-145">例如，产品包含的类别导航属性和类别包含产品导航属性。</span><span class="sxs-lookup"><span data-stu-id="c4724-145">For example, Product contains a Category navigation property and Category contains a Products navigation property.</span></span>

## <a name="poco"></a><span data-ttu-id="c4724-146">POCO</span><span class="sxs-lookup"><span data-stu-id="c4724-146">POCO</span></span>
<span data-ttu-id="c4724-147">纯旧式 CLR 对象的首字母缩写。</span><span class="sxs-lookup"><span data-stu-id="c4724-147">Acronym for Plain-Old CLR Object.</span></span> <span data-ttu-id="c4724-148">一个简单的用户类，并不依赖的任何框架。</span><span class="sxs-lookup"><span data-stu-id="c4724-148">A simple user class that has no dependencies with any framework.</span></span> <span data-ttu-id="c4724-149">在 EF 中，不是派生从 entityobject 继承、 实现任何接口或执行在 EF 中定义的任何特性的实体类的上下文。</span><span class="sxs-lookup"><span data-stu-id="c4724-149">In the context of EF, an entity class that does not derive from EntityObject, implements any interfaces or carries any attributes defined in EF.</span></span> <span data-ttu-id="c4724-150">此类从持久性框架分离的实体类也被称为是"持久性未知"。</span><span class="sxs-lookup"><span data-stu-id="c4724-150">Such entity classes that are decoupled from the persistence framework are also said to be "persistence ignorant".</span></span>  

## <a name="relationship-inverse"></a><span data-ttu-id="c4724-151">关系反转</span><span class="sxs-lookup"><span data-stu-id="c4724-151">Relationship inverse</span></span>
<span data-ttu-id="c4724-152">一种关系，例如，产品中相对的一端。类别和类别。产品。</span><span class="sxs-lookup"><span data-stu-id="c4724-152">The opposite end of a relationship, for example, product.Category and category.Product.</span></span>

## <a name="self-tracking-entity"></a><span data-ttu-id="c4724-153">自跟踪实体</span><span class="sxs-lookup"><span data-stu-id="c4724-153">Self-tracking entity</span></span>
<span data-ttu-id="c4724-154">从代码生成模板，使用 N 层开发可帮助生成实体。</span><span class="sxs-lookup"><span data-stu-id="c4724-154">An entity built from a code generation template that helps with N-Tier development.</span></span>

## <a name="table-per-concrete-type-tpc"></a><span data-ttu-id="c4724-155">表每个具体类型 (TPC)</span><span class="sxs-lookup"><span data-stu-id="c4724-155">Table-per-concrete type (TPC)</span></span>
<span data-ttu-id="c4724-156">一种映射继承层次结构中的每个非抽象类型映射到单独数据库中的表的其中一个方法。</span><span class="sxs-lookup"><span data-stu-id="c4724-156">A method of mapping the inheritance where each non-abstract type in the hierarchy is mapped to separate table in the database.</span></span>

## <a name="table-per-hierarchy-tph"></a><span data-ttu-id="c4724-157">每个层次结构一张表 (TPH)</span><span class="sxs-lookup"><span data-stu-id="c4724-157">Table-per-hierarchy (TPH)</span></span>
<span data-ttu-id="c4724-158">映射位置层次结构中的所有类型都映射到数据库中的同一个表继承的方法。</span><span class="sxs-lookup"><span data-stu-id="c4724-158">A method of mapping the inheritance where all types in the hierarchy are mapped to the same table in the database.</span></span> <span data-ttu-id="c4724-159">鉴别器列用于标识哪种类型的每一行与之关联。</span><span class="sxs-lookup"><span data-stu-id="c4724-159">A discriminator column(s) is used to identify what type each row is associated with.</span></span>

## <a name="table-per-type-tpt"></a><span data-ttu-id="c4724-160">每种类型一个表 (TPT)</span><span class="sxs-lookup"><span data-stu-id="c4724-160">Table-per-type (TPT)</span></span>
<span data-ttu-id="c4724-161">映射的继承层次结构中的所有类型的通用属性映射到在数据库中，同一个表，但唯一每种类型的属性映射到一个单独的表的方法。</span><span class="sxs-lookup"><span data-stu-id="c4724-161">A method of mapping the inheritance where the common properties of all types in the hierarchy are mapped to the same table in the database, but properties unique to each type are mapped to a separate table.</span></span>

## <a name="type-discovery"></a><span data-ttu-id="c4724-162">类型发现</span><span class="sxs-lookup"><span data-stu-id="c4724-162">Type discovery</span></span>
<span data-ttu-id="c4724-163">标识应该是实体框架模型的一部分的类型的过程。</span><span class="sxs-lookup"><span data-stu-id="c4724-163">The process of identifying the types that should be part of an Entity Framework model.</span></span>
