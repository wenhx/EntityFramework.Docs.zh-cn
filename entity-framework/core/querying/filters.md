---
title: 全局查询筛选器 - EF Core
author: anpete
ms.date: 11/03/2017
uid: core/querying/filters
ms.openlocfilehash: c9bbb8a5889834ea078ddb7e432863b3d0cf2ffe
ms.sourcegitcommit: 0cc9578fd49802789a00c0044b4e57325476ca2e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70271460"
---
# <a name="global-query-filters"></a><span data-ttu-id="cd818-102">全局查询筛选器</span><span class="sxs-lookup"><span data-stu-id="cd818-102">Global Query Filters</span></span>

> [!NOTE]
> <span data-ttu-id="cd818-103">EF Core 2.0 中已引入此功能。</span><span class="sxs-lookup"><span data-stu-id="cd818-103">This feature was introduced in EF Core 2.0.</span></span>

<span data-ttu-id="cd818-104">全局查询筛选器是应用于元数据模型（通常为 *OnModelCreating*）中的实体类型的 LINQ 查询谓词（通常传递给 LINQ *Where* 查询运算符的布尔表达式）。</span><span class="sxs-lookup"><span data-stu-id="cd818-104">Global query filters are LINQ query predicates (a boolean expression typically passed to the LINQ *Where* query operator) applied to Entity Types in the metadata model (usually in *OnModelCreating*).</span></span> <span data-ttu-id="cd818-105">此类筛选器自动应用于涉及这些实体类型（包括通过使用 Include 或直接导航属性引用等方式间接引用的实体类型）的所有 LINQ 查询。</span><span class="sxs-lookup"><span data-stu-id="cd818-105">Such filters are automatically applied to any LINQ queries involving those Entity Types, including Entity Types referenced indirectly, such as through the use of Include or direct navigation property references.</span></span> <span data-ttu-id="cd818-106">此功能的一些常见应用如下：</span><span class="sxs-lookup"><span data-stu-id="cd818-106">Some common applications of this feature are:</span></span>

* <span data-ttu-id="cd818-107">**软删除** - 实体类型定义“IsDeleted”  属性。</span><span class="sxs-lookup"><span data-stu-id="cd818-107">**Soft delete** - An Entity Type defines an *IsDeleted* property.</span></span>
* <span data-ttu-id="cd818-108">**多租户** - 实体类型定义“TenantId”  属性。</span><span class="sxs-lookup"><span data-stu-id="cd818-108">**Multi-tenancy** - An Entity Type defines a *TenantId* property.</span></span>

## <a name="example"></a><span data-ttu-id="cd818-109">示例</span><span class="sxs-lookup"><span data-stu-id="cd818-109">Example</span></span>

<span data-ttu-id="cd818-110">下面的示例显示了如何使用全局查询筛选器在简单的博客模型中实现软删除和多租户查询行为。</span><span class="sxs-lookup"><span data-stu-id="cd818-110">The following example shows how to use Global Query Filters to implement soft-delete and multi-tenancy query behaviors in a simple blogging model.</span></span>

> [!TIP]
> <span data-ttu-id="cd818-111">可在 GitHub 上查看此文章的[示例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters)。</span><span class="sxs-lookup"><span data-stu-id="cd818-111">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters) on GitHub.</span></span>

<span data-ttu-id="cd818-112">首先，定义实体：</span><span class="sxs-lookup"><span data-stu-id="cd818-112">First, define the entities:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Entities)]

<span data-ttu-id="cd818-113">请注意 _Blog_ 实体上的 _tenantId_ 字段的声明。</span><span class="sxs-lookup"><span data-stu-id="cd818-113">Note the declaration of a _tenantId_ field on the _Blog_ entity.</span></span> <span data-ttu-id="cd818-114">这会用于将每个 _Blog_ 实例与特定租户相关联。</span><span class="sxs-lookup"><span data-stu-id="cd818-114">This will be used to associate each Blog instance with a specific tenant.</span></span> <span data-ttu-id="cd818-115">同时在 _Post_ 实体类型上定义了 _IsDeleted_ 属性。</span><span class="sxs-lookup"><span data-stu-id="cd818-115">Also defined is an _IsDeleted_ property on the _Post_ entity type.</span></span> <span data-ttu-id="cd818-116">这会用于跟踪一个 _Post_ 实例是否已“软删除”。</span><span class="sxs-lookup"><span data-stu-id="cd818-116">This is used to keep track of whether a _Post_ instance has been "soft-deleted".</span></span> <span data-ttu-id="cd818-117">也就是说，实例只是被标记为已删除，而非真正删除了基础数据。</span><span class="sxs-lookup"><span data-stu-id="cd818-117">That is, the instance is marked as deleted without physically removing the underlying data.</span></span>

<span data-ttu-id="cd818-118">接下来，使用 `HasQueryFilter` API 在 _OnModelCreating_ 中配置查询筛选器。</span><span class="sxs-lookup"><span data-stu-id="cd818-118">Next, configure the query filters in _OnModelCreating_ using the `HasQueryFilter` API.</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Configuration)]

<span data-ttu-id="cd818-119">传递给 _HasQueryFilter_ 调用的谓词表达式将立即自动应用于这些类型的任何 LINQ 查询。</span><span class="sxs-lookup"><span data-stu-id="cd818-119">The predicate expressions passed to the _HasQueryFilter_ calls will now automatically be applied to any LINQ queries for those types.</span></span>

> [!TIP]
> <span data-ttu-id="cd818-120">请注意 DbContext 实例级别字段的使用：`_tenantId` 用于设置当前租户。</span><span class="sxs-lookup"><span data-stu-id="cd818-120">Note the use of a DbContext instance level field: `_tenantId` used to set the current tenant.</span></span> <span data-ttu-id="cd818-121">模型级筛选器将使用正确上下文实例（即执行查询的实例）中的值。</span><span class="sxs-lookup"><span data-stu-id="cd818-121">Model-level filters will use the value from the correct context instance (that is, the instance that is executing the query).</span></span>

> [!NOTE]
> <span data-ttu-id="cd818-122">目前不能在同一个实体中定义多个查询筛选器，只会应用最后一个筛选器。</span><span class="sxs-lookup"><span data-stu-id="cd818-122">It is currently not possible to define multiple query filters on the same entity - only the last one will be applied.</span></span> <span data-ttu-id="cd818-123">但是，可以使用逻辑 _AND_ 运算符（[C# 中为 `&&`](https://docs.microsoft.com/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)）定义含有多种条件的单个筛选器。</span><span class="sxs-lookup"><span data-stu-id="cd818-123">However, you can define a single filter with multiple conditions using the logical _AND_ operator ([`&&` in C#](https://docs.microsoft.com/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).</span></span>

## <a name="disabling-filters"></a><span data-ttu-id="cd818-124">禁用筛选器</span><span class="sxs-lookup"><span data-stu-id="cd818-124">Disabling Filters</span></span>

<span data-ttu-id="cd818-125">可使用 `IgnoreQueryFilters()` 运算符对各个 LINQ 查询禁用筛选器。</span><span class="sxs-lookup"><span data-stu-id="cd818-125">Filters may be disabled for individual LINQ queries by using the `IgnoreQueryFilters()` operator.</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a><span data-ttu-id="cd818-126">限制</span><span class="sxs-lookup"><span data-stu-id="cd818-126">Limitations</span></span>

<span data-ttu-id="cd818-127">全局查询筛选器具有以下限制：</span><span class="sxs-lookup"><span data-stu-id="cd818-127">Global query filters have the following limitations:</span></span>

* <span data-ttu-id="cd818-128">筛选器不能包含对导航属性的引用。</span><span class="sxs-lookup"><span data-stu-id="cd818-128">Filters cannot contain references to navigation properties.</span></span>
* <span data-ttu-id="cd818-129">仅可为继承层次结构的根实体类型定义筛选器。</span><span class="sxs-lookup"><span data-stu-id="cd818-129">Filters can only be defined for the root Entity Type of an inheritance hierarchy.</span></span>
