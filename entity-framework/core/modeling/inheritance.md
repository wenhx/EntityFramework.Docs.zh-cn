---
title: 继承-EF Core
description: 如何使用 Entity Framework Core 配置实体类型继承
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 10/27/2016
uid: core/modeling/inheritance
ms.openlocfilehash: 0e94013a0b894b162f4bb3ca8e7acb1aca349011
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664047"
---
# <a name="inheritance"></a><span data-ttu-id="29608-103">继承</span><span class="sxs-lookup"><span data-stu-id="29608-103">Inheritance</span></span>

<span data-ttu-id="29608-104">EF 可以将 .NET 类型层次结构映射到数据库。</span><span class="sxs-lookup"><span data-stu-id="29608-104">EF can map a .NET type hierarchy to a database.</span></span> <span data-ttu-id="29608-105">这使你可以像平常一样使用基类型和派生类型在代码中编写 .NET 实体，并让 EF 无缝创建适当的数据库架构、发出查询等。类型层次结构的映射方式的实际详细信息与提供程序相关;本页介绍关系数据库上下文中的继承支持。</span><span class="sxs-lookup"><span data-stu-id="29608-105">This allows you to write your .NET entities in code as usual, using base and derived types, and have EF seamlessly create the appropriate database schema, issue queries, etc. The actual details of how a type hierarchy is mapped are provider-dependent; this page describes inheritance support in the context of a relational database.</span></span>

<span data-ttu-id="29608-106">目前，EF Core 仅支持每个层次结构一个表（TPH）模式。</span><span class="sxs-lookup"><span data-stu-id="29608-106">At the moment, EF Core only supports the table-per-hierarchy (TPH) pattern.</span></span> <span data-ttu-id="29608-107">TPH 使用单个表来存储层次结构中所有类型的数据，而鉴别器列用于标识每行所表示的类型。</span><span class="sxs-lookup"><span data-stu-id="29608-107">TPH uses a single table to store the data for all types in the hierarchy, and a discriminator column is used to identify which type each row represents.</span></span>

> [!NOTE]
> <span data-ttu-id="29608-108">EF Core 尚不支持 EF6 支持每种类型一个表（TPT）和每个具体的表类型（TPC）。</span><span class="sxs-lookup"><span data-stu-id="29608-108">The table-per-type (TPT) and table-per-concrete-type (TPC), which are supported by EF6, are not yet supported by EF Core.</span></span> <span data-ttu-id="29608-109">TPT 是为 EF Core 5.0 计划的主要功能。</span><span class="sxs-lookup"><span data-stu-id="29608-109">TPT is a major feature planned for EF Core 5.0.</span></span>

## <a name="entity-type-hierarchy-mapping"></a><span data-ttu-id="29608-110">实体类型层次结构映射</span><span class="sxs-lookup"><span data-stu-id="29608-110">Entity type hierarchy mapping</span></span>

<span data-ttu-id="29608-111">按照约定，EF 不会自动扫描基类型或派生类型;这意味着，如果要映射层次结构中的 CLR 类型，则必须在模型上显式指定该类型。</span><span class="sxs-lookup"><span data-stu-id="29608-111">By convention, EF will not automatically scan for base or derived types; this means that if you want a CLR type in your hierarchy to be mapped, you must explicitly specify that type on your model.</span></span> <span data-ttu-id="29608-112">例如，仅指定层次结构的基类型将不会导致 EF Core 隐式包含其所有子类型。</span><span class="sxs-lookup"><span data-stu-id="29608-112">For example, specifying only the base type of a hierarchy will not cause EF Core to implicitly include all of its sub-types.</span></span>

<span data-ttu-id="29608-113">下面的示例为及其子类公开了 DbSet `Blog` `RssBlog` 。</span><span class="sxs-lookup"><span data-stu-id="29608-113">The following sample exposes a DbSet for `Blog` and its subclass `RssBlog`.</span></span> <span data-ttu-id="29608-114">如果 `Blog` 有任何其他子类，则不会将其包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="29608-114">If `Blog` has any other subclass, it will not be included in the model.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs?name=InheritanceDbSets&highlight=3-4)]

<span data-ttu-id="29608-115">此模型映射到下面的数据库架构（请注意隐式创建的*鉴别*器列，该列标识每个行中存储的*博客*类型）：</span><span class="sxs-lookup"><span data-stu-id="29608-115">This model is mapped to the following database schema (note the implicitly-created *Discriminator* column, which identifies which type of *Blog* is stored in each row):</span></span>

![image](_static/inheritance-tph-data.png)

>[!NOTE]
> <span data-ttu-id="29608-117">使用 TPH 映射时，数据库列会根据需要自动进行为 null。</span><span class="sxs-lookup"><span data-stu-id="29608-117">Database columns are automatically made nullable as necessary when using TPH mapping.</span></span> <span data-ttu-id="29608-118">例如， *RssUrl*列可以为 null，因为常规*博客*实例不具有该属性。</span><span class="sxs-lookup"><span data-stu-id="29608-118">For example, the *RssUrl* column is nullable because regular *Blog* instances do not have that property.</span></span>

<span data-ttu-id="29608-119">如果不想公开层次结构中一个或多个实体的 DbSet，还可以使用熟知的 API 来确保它们包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="29608-119">If you don't want to expose a DbSet for one or more entities in the hierarchy, you can also use the Fluent API to ensure they are included in the model.</span></span>

> [!TIP]
> <span data-ttu-id="29608-120">如果不依赖约定，可以使用显式指定基类型 `HasBaseType` 。</span><span class="sxs-lookup"><span data-stu-id="29608-120">If you don't rely on conventions, you can specify the base type explicitly using `HasBaseType`.</span></span> <span data-ttu-id="29608-121">你还可以使用 `.HasBaseType((Type)null)` 从层次结构中删除实体类型。</span><span class="sxs-lookup"><span data-stu-id="29608-121">You can also use `.HasBaseType((Type)null)` to remove an entity type from the hierarchy.</span></span>

## <a name="discriminator-configuration"></a><span data-ttu-id="29608-122">鉴别器配置</span><span class="sxs-lookup"><span data-stu-id="29608-122">Discriminator configuration</span></span>

<span data-ttu-id="29608-123">您可以配置鉴别器列的名称和类型以及用于标识层次结构中的每种类型的值：</span><span class="sxs-lookup"><span data-stu-id="29608-123">You can configure the name and type of the discriminator column and the values that are used to identify each type in the hierarchy:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorConfiguration.cs?name=DiscriminatorConfiguration&highlight=4-6)]

<span data-ttu-id="29608-124">在上述示例中，EF 在层次结构的基实体上将鉴别器隐式添加为[影子属性](xref:core/modeling/shadow-properties)。</span><span class="sxs-lookup"><span data-stu-id="29608-124">In the examples above, EF added the discriminator implicitly as a [shadow property](xref:core/modeling/shadow-properties) on the base entity of the hierarchy.</span></span> <span data-ttu-id="29608-125">此属性可以配置为类似于任何其他属性：</span><span class="sxs-lookup"><span data-stu-id="29608-125">This property can be configured like any other:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorPropertyConfiguration.cs?name=DiscriminatorPropertyConfiguration&highlight=4-5)]

<span data-ttu-id="29608-126">最后，鉴别器还可以映射到实体中的常规 .NET 属性：</span><span class="sxs-lookup"><span data-stu-id="29608-126">Finally, the discriminator can also be mapped to a regular .NET property in your entity:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/NonShadowDiscriminator.cs?name=NonShadowDiscriminator&highlight=4)]

## <a name="shared-columns"></a><span data-ttu-id="29608-127">共享列</span><span class="sxs-lookup"><span data-stu-id="29608-127">Shared columns</span></span>

<span data-ttu-id="29608-128">默认情况下，当层次结构中的两个同级实体类型具有相同的属性时，它们将映射到两个单独的列。</span><span class="sxs-lookup"><span data-stu-id="29608-128">By default, when two sibling entity types in the hierarchy have a property with the same name, they will be mapped to two separate columns.</span></span> <span data-ttu-id="29608-129">但是，如果它们的类型相同，则可以映射到相同的数据库列：</span><span class="sxs-lookup"><span data-stu-id="29608-129">However, if their type is identical they can be mapped to the same database column:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedTPHColumns.cs?name=SharedTPHColumns&highlight=9,13)]
