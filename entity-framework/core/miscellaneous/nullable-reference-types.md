---
title: 使用可以为 null 的引用类型-EF Core
author: roji
ms.date: 09/09/2019
ms.assetid: bde4e0ee-fba3-4813-a849-27049323d301
uid: core/miscellaneous/nullable-reference-types
ms.openlocfilehash: 0d05902566b6b166f1267915d9f698ed29dff588
ms.sourcegitcommit: 32c51c22988c6f83ed4f8e50a1d01be3f4114e81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/27/2019
ms.locfileid: "75502062"
---
# <a name="working-with-nullable-reference-types"></a><span data-ttu-id="dcc94-102">使用可以为 Null 的引用类型</span><span class="sxs-lookup"><span data-stu-id="dcc94-102">Working with Nullable Reference Types</span></span>

<span data-ttu-id="dcc94-103">C#8引入了一种名[为 null 的引用类型](/dotnet/csharp/tutorials/nullable-reference-types)的新功能，允许对引用类型进行批注，以指示它是否可用于包含 null。</span><span class="sxs-lookup"><span data-stu-id="dcc94-103">C# 8 introduced a new feature called [nullable reference types](/dotnet/csharp/tutorials/nullable-reference-types), allowing reference types to be annotated, indicating whether it is valid for them to contain null or not.</span></span> <span data-ttu-id="dcc94-104">如果你不熟悉此功能，则建议你通过阅读C#文档来使自己熟悉该功能。</span><span class="sxs-lookup"><span data-stu-id="dcc94-104">If you are new to this feature, it is recommended that make yourself familiar with it by reading the C# docs.</span></span>

<span data-ttu-id="dcc94-105">此页介绍 EF Core 对可为 null 的引用类型的支持，并介绍了使用它们的最佳做法。</span><span class="sxs-lookup"><span data-stu-id="dcc94-105">This page introduces EF Core's support for nullable reference types, and describes best practices for working with them.</span></span>

## <a name="required-and-optional-properties"></a><span data-ttu-id="dcc94-106">必需属性和可选属性</span><span class="sxs-lookup"><span data-stu-id="dcc94-106">Required and optional properties</span></span>

<span data-ttu-id="dcc94-107">对于必需属性和可选属性及其与可为 null 的引用类型的交互，主要文档是[必需的和可选的属性](xref:core/modeling/entity-properties#required-and-optional-properties)页。</span><span class="sxs-lookup"><span data-stu-id="dcc94-107">The main documentation on required and optional properties and their interaction with nullable reference types is the [Required and Optional Properties](xref:core/modeling/entity-properties#required-and-optional-properties) page.</span></span> <span data-ttu-id="dcc94-108">建议首先阅读该页面。</span><span class="sxs-lookup"><span data-stu-id="dcc94-108">It is recommended you start out by reading that page first.</span></span>

> [!NOTE]
> <span data-ttu-id="dcc94-109">在现有项目上启用可以为 null 的引用类型时要格外小心：现在配置为可选的引用类型属性现在将配置为 "必需"，除非它们显式批注为可为 null。</span><span class="sxs-lookup"><span data-stu-id="dcc94-109">Exercise caution when enabling nullable reference types on an existing project: reference type properties which were previously configured as optional will now be configured as required, unless they are explicitly annotated to be nullable.</span></span> <span data-ttu-id="dcc94-110">管理关系数据库架构时，这可能会导致生成更改数据库列的为 null 性的迁移。</span><span class="sxs-lookup"><span data-stu-id="dcc94-110">When managing a relational database schema, this may cause migrations to be generated which alter the database column's nullability.</span></span>

## <a name="dbcontext-and-dbset"></a><span data-ttu-id="dcc94-111">DbContext 和 DbSet</span><span class="sxs-lookup"><span data-stu-id="dcc94-111">DbContext and DbSet</span></span>

<span data-ttu-id="dcc94-112">如果启用了可以为 null 的引用C#类型，则编译器将为任何未初始化的不可为 null 的属性发出警告，因为这将包含 null。</span><span class="sxs-lookup"><span data-stu-id="dcc94-112">When nullable reference types are enabled, the C# compiler emits warnings for any uninitialized non-nullable property, as these would contain null.</span></span> <span data-ttu-id="dcc94-113">因此，在上下文中定义不可为 null 的 `DbSet` 的常见做法现在会生成一个警告。</span><span class="sxs-lookup"><span data-stu-id="dcc94-113">As a result, the common practice of defining a non-nullable `DbSet` on a context will now generate a warning.</span></span> <span data-ttu-id="dcc94-114">但是，EF Core 始终初始化 DbContext 派生类型上的所有 `DbSet` 属性，因此即使编译器不知道此操作，也可以保证它们永远不会为 null。</span><span class="sxs-lookup"><span data-stu-id="dcc94-114">However, EF Core always initializes all `DbSet` properties on DbContext-derived types, so they are guaranteed to never be null, even if the compiler is unaware of this.</span></span> <span data-ttu-id="dcc94-115">因此，建议保留不可以为 null 的 `DbSet` 属性，使你能够在不进行 null 检查的情况下访问这些属性，并通过使用 null 包容性运算符（！）的帮助将其显式设置为 null 来使编译器警告静音：</span><span class="sxs-lookup"><span data-stu-id="dcc94-115">Therefore, it is recommended to keep your `DbSet` properties non-nullable - allowing you to access them without null checks - and to silence the compiler warnings by explicitly setting them to null with the help of the null-forgiving operator (!):</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/NullableReferenceTypesContext.cs?name=Context&highlight=3-4)]

## <a name="non-nullable-properties-and-initialization"></a><span data-ttu-id="dcc94-116">不可以为 null 的属性和初始化</span><span class="sxs-lookup"><span data-stu-id="dcc94-116">Non-nullable properties and initialization</span></span>

<span data-ttu-id="dcc94-117">对于实体类型上的常规属性，未初始化的不可以为 null 的引用类型的编译器警告也是一个问题。</span><span class="sxs-lookup"><span data-stu-id="dcc94-117">Compiler warnings for uninitialized non-nullable reference types are also a problem for regular properties on your entity types.</span></span> <span data-ttu-id="dcc94-118">在上面的示例中，我们使用[构造函数绑定](xref:core/modeling/constructors)避免了这些警告，这是一项完美使用不可为 null 的属性的功能，确保它们始终初始化。</span><span class="sxs-lookup"><span data-stu-id="dcc94-118">In our example above, we avoided these warnings by using [constructor binding](xref:core/modeling/constructors), a feature which works perfectly with non-nullable properties, ensuring they are always initialized.</span></span> <span data-ttu-id="dcc94-119">但是，在某些情况下，构造函数绑定不是一个选项：例如，不能以这种方式初始化导航属性。</span><span class="sxs-lookup"><span data-stu-id="dcc94-119">However, in some scenarios constructor binding isn't an option: navigation properties, for example, cannot be initialized in this way.</span></span>

<span data-ttu-id="dcc94-120">所需的导航属性会带来额外的难度：尽管某个给定主体的依赖项始终存在，但特定查询可能会或不加载该依赖项，具体取决于程序中该点的需求（[请参阅不同的加载数据模式](xref:core/querying/related-data)）。</span><span class="sxs-lookup"><span data-stu-id="dcc94-120">Required navigation properties present an additional difficulty: although a dependent will always exist for a given principal, it may or may not be loaded by a particular query, depending on the needs at that point in the program ([see the different patterns for loading data](xref:core/querying/related-data)).</span></span> <span data-ttu-id="dcc94-121">同时，不需要将这些属性设置为可以为 null，因为这会强制对它们的所有访问权限，以检查是否有 null，即使它们是必需的。</span><span class="sxs-lookup"><span data-stu-id="dcc94-121">At the same time, it is undesirable to make these properties nullable, since that would force all access to them to check for null, even if they are required.</span></span>

<span data-ttu-id="dcc94-122">处理这些方案的一种方法是使用一个可以为 null 的[支持字段](xref:core/modeling/backing-field)的不可为 null 的属性：</span><span class="sxs-lookup"><span data-stu-id="dcc94-122">One way to deal with these scenarios, is to have a non-nullable property with a nullable [backing field](xref:core/modeling/backing-field):</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Order.cs?range=12-17)]

<span data-ttu-id="dcc94-123">由于导航属性不可为 null，因此配置了必需的导航;只要正确加载导航，就可以通过属性访问依赖项。</span><span class="sxs-lookup"><span data-stu-id="dcc94-123">Since the navigation property is non-nullable, a required navigation is configured; and as long as the navigation is properly loaded, the dependent will be accessible via the property.</span></span> <span data-ttu-id="dcc94-124">但是，如果在未事先正确加载相关实体的情况下访问属性，则会引发 InvalidOperationException，因为 API 协定的使用不正确。</span><span class="sxs-lookup"><span data-stu-id="dcc94-124">If, however, the property is accessed without first properly loading the related entity, an InvalidOperationException is thrown, since the API contract has been used incorrectly.</span></span>

<span data-ttu-id="dcc94-125">作为 terser 的替代方法，可以使用包容性运算符（！）的帮助简单地将属性初始化为 null：</span><span class="sxs-lookup"><span data-stu-id="dcc94-125">As a terser alternative, it is possible to simply initialize the property to null with the help of the null-forgiving operator (!):</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Order.cs?range=19)]

<span data-ttu-id="dcc94-126">实际的 null 值永远不会被视为除了编程错误之外的情况，例如，访问导航属性时，无需事先正确加载相关实体。</span><span class="sxs-lookup"><span data-stu-id="dcc94-126">An actual null value will never be observed except as a result of a programming bug, e.g. accessing the navigation property without properly loading the related entity beforehand.</span></span>

> [!NOTE]
> <span data-ttu-id="dcc94-127">包含对多个相关实体的引用的集合导航应始终不可为 null。</span><span class="sxs-lookup"><span data-stu-id="dcc94-127">Collection navigations, which contain references to multiple related entities, should always be non-nullable.</span></span> <span data-ttu-id="dcc94-128">空集合意味着不存在相关实体，但列表本身不应为 null。</span><span class="sxs-lookup"><span data-stu-id="dcc94-128">An empty collection means that no related entities exist, but the list itself should never be null.</span></span>

## <a name="navigating-and-including-nullable-relationships"></a><span data-ttu-id="dcc94-129">导航和包括可以为 null 的关系</span><span class="sxs-lookup"><span data-stu-id="dcc94-129">Navigating and including nullable relationships</span></span>

<span data-ttu-id="dcc94-130">当处理可选关系时，可能会遇到编译器警告，但不可能出现实际的 null 引用异常。</span><span class="sxs-lookup"><span data-stu-id="dcc94-130">When dealing with optional relationships, it's possible to encounter compiler warnings where an actual null reference exception would be impossible.</span></span> <span data-ttu-id="dcc94-131">在转换和执行 LINQ 查询时，EF Core 确保在一个可选的相关实体不存在的情况下，将忽略对该实体的任何导航，而不是引发。</span><span class="sxs-lookup"><span data-stu-id="dcc94-131">When translating and executing your LINQ queries, EF Core guarantees that if an optional related entity does not exist, any navigation to it will simply be ignored, rather than throwing.</span></span> <span data-ttu-id="dcc94-132">但是，编译器不知道这 EF Core 确保，并生成警告，就好像 LINQ 查询是在内存中执行的，而 LINQ to Objects。</span><span class="sxs-lookup"><span data-stu-id="dcc94-132">However, the compiler is unaware of this EF Core guarantee, and produces warnings as if the LINQ query were executed in memory, with LINQ to Objects.</span></span> <span data-ttu-id="dcc94-133">因此，需要使用包容性运算符（！）来通知编译器无法实现实际的 null 值：</span><span class="sxs-lookup"><span data-stu-id="dcc94-133">As a result, it is necessary to use the null-forgiving operator (!) to inform the compiler that an actual null value isn't possible:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Program.cs?range=46)]

<span data-ttu-id="dcc94-134">在可选导航中包含多个级别的关系时，会发生类似的问题：</span><span class="sxs-lookup"><span data-stu-id="dcc94-134">A similar issue occurs when including multiple levels of relationships across optional navigations:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Program.cs?range=36-39&highlight=2)]

<span data-ttu-id="dcc94-135">如果你发现自己执行了很多操作，并且所涉及的实体类型是在 EF Core 查询中主要（或独占）使用的，请考虑使导航属性不可为 null，并将其配置为可通过熟知 API 或数据批注进行选择。</span><span class="sxs-lookup"><span data-stu-id="dcc94-135">If you find yourself doing this a lot, and the entity types in question are predominantly (or exclusively) used in EF Core queries, consider making the navigation properties non-nullable, and to configure them as optional via the Fluent API or Data Annotations.</span></span> <span data-ttu-id="dcc94-136">这将删除所有编译器警告，同时保持关系为可选;但是，如果你的实体是在 EF Core 之外遍历的，则你可能会观察到 null 值，尽管这些属性已批注为不可为 null。</span><span class="sxs-lookup"><span data-stu-id="dcc94-136">This will remove all compiler warnings while keeping the relationship optional; however, if your entities are traversed outside of EF Core, you may observe null values although the properties are annotated as non-nullable.</span></span>

## <a name="scaffolding"></a><span data-ttu-id="dcc94-137">基架</span><span class="sxs-lookup"><span data-stu-id="dcc94-137">Scaffolding</span></span>

<span data-ttu-id="dcc94-138">[反向C#工程当前不支持8可为 null 的引用类型功能](/dotnet/csharp/tutorials/nullable-reference-types)： EF Core 始终C#会生成假定功能已关闭的代码。</span><span class="sxs-lookup"><span data-stu-id="dcc94-138">[The C# 8 nullable reference type feature](/dotnet/csharp/tutorials/nullable-reference-types) is currently unsupported in reverse engineering: EF Core always generates C# code that assumes the feature is off.</span></span> <span data-ttu-id="dcc94-139">例如，可为 null 的文本列将被基架为具有类型 `string` 的属性，而不是 `string?`，其中使用的是用于配置是否需要属性的流畅 API 或数据批注。</span><span class="sxs-lookup"><span data-stu-id="dcc94-139">For example, nullable text columns will be scaffolded as a property with type `string` , not `string?`, with either the Fluent API or Data Annotations used to configure whether a property is required or not.</span></span> <span data-ttu-id="dcc94-140">您可以编辑基架代码并将其替换为C#可为 null 的批注。</span><span class="sxs-lookup"><span data-stu-id="dcc94-140">You can edit the scaffolded code and replace these with C# nullability annotations.</span></span> <span data-ttu-id="dcc94-141">[#15520](https://github.com/aspnet/EntityFrameworkCore/issues/15520)的问题跟踪了可为 null 的引用类型的基架支持。</span><span class="sxs-lookup"><span data-stu-id="dcc94-141">Scaffolding support for nullable reference types is tracked by issue [#15520](https://github.com/aspnet/EntityFrameworkCore/issues/15520).</span></span>
