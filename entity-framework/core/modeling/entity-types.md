---
title: 实体类型-EF Core
description: 如何使用 Entity Framework Core 配置和映射实体类型
author: roji
ms.date: 12/03/2019
ms.assetid: cbe6935e-2679-4b77-8914-a8d772240cf1
uid: core/modeling/entity-types
ms.openlocfilehash: b3d9ad753637d021d9aa52965da38091ae690f77
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414577"
---
# <a name="entity-types"></a><span data-ttu-id="230a3-103">实体类型</span><span class="sxs-lookup"><span data-stu-id="230a3-103">Entity Types</span></span>

<span data-ttu-id="230a3-104">在上下文中包含一种类型的 DbSet，这意味着它包含在 EF Core 的模型中;我们通常将此类类型称为*实体*。</span><span class="sxs-lookup"><span data-stu-id="230a3-104">Including a DbSet of a type on your context means that it is included in EF Core's model; we usually refer to such a type as an *entity*.</span></span> <span data-ttu-id="230a3-105">EF Core 可以从/向数据库中读取和写入实体实例，如果使用的是关系数据库，EF Core 可以通过迁移为实体创建表。</span><span class="sxs-lookup"><span data-stu-id="230a3-105">EF Core can read and write entity instances from/to the database, and if you're using a relational database, EF Core can create tables for your entities via migrations.</span></span>

## <a name="including-types-in-the-model"></a><span data-ttu-id="230a3-106">在模型中包含类型</span><span class="sxs-lookup"><span data-stu-id="230a3-106">Including types in the model</span></span>

<span data-ttu-id="230a3-107">按照约定，在上下文中的 DbSet 属性中公开的类型作为实体包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="230a3-107">By convention, types that are exposed in DbSet properties on your context are included in the model as entities.</span></span> <span data-ttu-id="230a3-108">还包括在 `OnModelCreating` 方法中指定的实体类型，就像通过递归方式浏览其他发现的实体类型的导航属性找到的任何类型一样。</span><span class="sxs-lookup"><span data-stu-id="230a3-108">Entity types that are specified in the `OnModelCreating` method are also included, as are any types that are found by recursively exploring the navigation properties of other discovered entity types.</span></span>

<span data-ttu-id="230a3-109">在下面的代码示例中，包含了所有类型：</span><span class="sxs-lookup"><span data-stu-id="230a3-109">In the code sample below, all types are included:</span></span>

* <span data-ttu-id="230a3-110">`Blog` 包含在内，因为它在上下文的 DbSet 属性中公开。</span><span class="sxs-lookup"><span data-stu-id="230a3-110">`Blog` is included because it's exposed in a DbSet property on the context.</span></span>
* <span data-ttu-id="230a3-111">包含 `Post` 是因为它是通过 `Blog.Posts` 导航属性发现的。</span><span class="sxs-lookup"><span data-stu-id="230a3-111">`Post` is included because it's discovered via the `Blog.Posts` navigation property.</span></span>
* <span data-ttu-id="230a3-112">`AuditEntry`，因为它是在 `OnModelCreating`中指定的。</span><span class="sxs-lookup"><span data-stu-id="230a3-112">`AuditEntry` because it is specified in `OnModelCreating`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs?name=EntityTypes&highlight=3,7,16)]

## <a name="excluding-types-from-the-model"></a><span data-ttu-id="230a3-113">从模型中排除类型</span><span class="sxs-lookup"><span data-stu-id="230a3-113">Excluding types from the model</span></span>

<span data-ttu-id="230a3-114">如果您不希望在模型中包含某一类型，则可以排除它：</span><span class="sxs-lookup"><span data-stu-id="230a3-114">If you don't want a type to be included in the model, you can exclude it:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="230a3-115">数据注释</span><span class="sxs-lookup"><span data-stu-id="230a3-115">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreType.cs?name=IgnoreType&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="230a3-116">熟知 API</span><span class="sxs-lookup"><span data-stu-id="230a3-116">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreType.cs?name=IgnoreType&highlight=3)]

***

## <a name="table-name"></a><span data-ttu-id="230a3-117">表名</span><span class="sxs-lookup"><span data-stu-id="230a3-117">Table name</span></span>

<span data-ttu-id="230a3-118">按照约定，每个实体类型将设置为映射到与公开实体的 DbSet 属性同名的数据库表。</span><span class="sxs-lookup"><span data-stu-id="230a3-118">By convention, each entity type will be set up to map to a database table with the same name as the DbSet property that exposes the entity.</span></span> <span data-ttu-id="230a3-119">如果给定实体不存在 DbSet，则使用类名称。</span><span class="sxs-lookup"><span data-stu-id="230a3-119">If no DbSet exists for the given entity, the class name is used.</span></span>

<span data-ttu-id="230a3-120">可以手动配置表名：</span><span class="sxs-lookup"><span data-stu-id="230a3-120">You can manually configure the table name:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="230a3-121">数据注释</span><span class="sxs-lookup"><span data-stu-id="230a3-121">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableName.cs?Name=TableName&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="230a3-122">熟知 API</span><span class="sxs-lookup"><span data-stu-id="230a3-122">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableName.cs?Name=TableName&highlight=3-4)]

***

## <a name="table-schema"></a><span data-ttu-id="230a3-123">表架构</span><span class="sxs-lookup"><span data-stu-id="230a3-123">Table schema</span></span>

<span data-ttu-id="230a3-124">使用关系数据库时，表按约定在数据库的默认架构中创建。</span><span class="sxs-lookup"><span data-stu-id="230a3-124">When using a relational database, tables are by convention created in your database's default schema.</span></span> <span data-ttu-id="230a3-125">例如，Microsoft SQL Server 将使用 `dbo` 架构（SQLite 不支持架构）。</span><span class="sxs-lookup"><span data-stu-id="230a3-125">For example, Microsoft SQL Server will use the `dbo` schema (SQLite does not support schemas).</span></span>

<span data-ttu-id="230a3-126">你可以配置要在特定架构中创建的表，如下所示：</span><span class="sxs-lookup"><span data-stu-id="230a3-126">You can configure tables to be created in a specific schema as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="230a3-127">数据注释</span><span class="sxs-lookup"><span data-stu-id="230a3-127">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="230a3-128">熟知 API</span><span class="sxs-lookup"><span data-stu-id="230a3-128">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=3-4)]

***

<span data-ttu-id="230a3-129">您还可以在模型级别定义 Fluent API 的默认架构，而不是为每个表指定架构：</span><span class="sxs-lookup"><span data-stu-id="230a3-129">Rather than specifying the schema for each table, you can also define the default schema at the model level with the fluent API:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultSchema.cs?name=DefaultSchema&highlight=3)]

<span data-ttu-id="230a3-130">请注意，设置默认架构也会影响其他数据库对象，如序列。</span><span class="sxs-lookup"><span data-stu-id="230a3-130">Note that setting the default schema will also affect other database objects, such as sequences.</span></span>
