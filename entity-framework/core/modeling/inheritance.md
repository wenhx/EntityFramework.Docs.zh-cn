---
title: 继承-EF Core
description: 如何使用 Entity Framework Core 配置实体类型继承
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 10/27/2016
uid: core/modeling/inheritance
ms.openlocfilehash: 507854e3acc0347adee612e516b3e2e0b10f55cf
ms.sourcegitcommit: 32c51c22988c6f83ed4f8e50a1d01be3f4114e81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/27/2019
ms.locfileid: "75502158"
---
# <a name="inheritance"></a><span data-ttu-id="feb2b-103">继承</span><span class="sxs-lookup"><span data-stu-id="feb2b-103">Inheritance</span></span>

<span data-ttu-id="feb2b-104">EF 可以将 .NET 类型层次结构映射到数据库。</span><span class="sxs-lookup"><span data-stu-id="feb2b-104">EF can map a .NET type hierarchy to a database.</span></span> <span data-ttu-id="feb2b-105">这使你可以像平常一样使用基类型和派生类型在代码中编写 .NET 实体，并让 EF 无缝创建适当的数据库架构、发出查询等。类型层次结构的映射方式的实际详细信息与提供程序相关;本页介绍关系数据库上下文中的继承支持。</span><span class="sxs-lookup"><span data-stu-id="feb2b-105">This allows you to write your .NET entities in code as usual, using base and derived types, and have EF seamlessly create the appropriate database schema, issue queries, etc. The actual details of how a type hierarchy is mapped are provider-dependent; this page describes inheritance support in the context of a relational database.</span></span>

<span data-ttu-id="feb2b-106">目前，EF Core 仅支持每个层次结构一个表（TPH）模式。</span><span class="sxs-lookup"><span data-stu-id="feb2b-106">At the moment, EF Core only supports the table-per-hierarchy (TPH) pattern.</span></span> <span data-ttu-id="feb2b-107">TPH 使用单个表来存储层次结构中所有类型的数据，而鉴别器列用于标识每行所表示的类型。</span><span class="sxs-lookup"><span data-stu-id="feb2b-107">TPH uses a single table to store the data for all types in the hierarchy, and a discriminator column is used to identify which type each row represents.</span></span>

> [!NOTE]
> <span data-ttu-id="feb2b-108">EF Core 尚不支持 EF6 支持每种类型一个表（TPT）和每个具体的表类型（TPC）。</span><span class="sxs-lookup"><span data-stu-id="feb2b-108">The table-per-type (TPT) and table-per-concrete-type (TPC), which are supported by EF6, are not yet supported by EF Core.</span></span> <span data-ttu-id="feb2b-109">TPT 是为 EF Core 5.0 计划的主要功能。</span><span class="sxs-lookup"><span data-stu-id="feb2b-109">TPT is a major feature planned for EF Core 5.0.</span></span>

## <a name="entity-type-hierarchy-mapping"></a><span data-ttu-id="feb2b-110">实体类型层次结构映射</span><span class="sxs-lookup"><span data-stu-id="feb2b-110">Entity type hierarchy mapping</span></span>

<span data-ttu-id="feb2b-111">按照约定，EF 仅会在模型中显式包括两个或更多个继承类型时设置继承。</span><span class="sxs-lookup"><span data-stu-id="feb2b-111">By convention, EF will only set up inheritance if two or more inherited types are explicitly included in the model.</span></span> <span data-ttu-id="feb2b-112">EF 不会自动扫描未在模型中包括的基类型或派生类型。</span><span class="sxs-lookup"><span data-stu-id="feb2b-112">EF will not automatically scan for base or derived types that are not otherwise included in the model.</span></span>

<span data-ttu-id="feb2b-113">可以通过为继承层次结构中的每个类型公开 DbSet，在模型中包括类型：</span><span class="sxs-lookup"><span data-stu-id="feb2b-113">You can include types in the model by exposing a DbSet for each type in the inheritance hierarchy:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs?name=InheritanceDbSets&highlight=3-4)]

<span data-ttu-id="feb2b-114">此模型映射到下面的数据库架构（请注意隐式创建的*鉴别*器列，该列标识每个行中存储的*博客*类型）：</span><span class="sxs-lookup"><span data-stu-id="feb2b-114">This model be mapped to the following database schema (note the implicitly-created *Discriminator* column, which identifies which type of *Blog* is stored in each row):</span></span>

![图像](_static/inheritance-tph-data.png)

>[!NOTE]
> <span data-ttu-id="feb2b-116">使用 TPH 映射时，数据库列会根据需要自动进行为 null。</span><span class="sxs-lookup"><span data-stu-id="feb2b-116">Database columns are automatically made nullable as necessary when using TPH mapping.</span></span> <span data-ttu-id="feb2b-117">例如， *RssUrl*列可以为 null，因为常规*博客*实例不具有该属性。</span><span class="sxs-lookup"><span data-stu-id="feb2b-117">For example, the *RssUrl* column is nullable because regular *Blog* instances do not have that property.</span></span>

<span data-ttu-id="feb2b-118">如果不想公开层次结构中一个或多个实体的 DbSet，还可以使用熟知的 API 来确保它们包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="feb2b-118">If you don't want to expose a DbSet for one or more entities in the hierarchy, you can also use the Fluent API to ensure they are included in the model.</span></span>

> [!TIP]
> <span data-ttu-id="feb2b-119">如果不依赖约定，可以使用 `HasBaseType`显式指定基类型。</span><span class="sxs-lookup"><span data-stu-id="feb2b-119">If you don't rely on conventions, you can specify the base type explicitly using `HasBaseType`.</span></span> <span data-ttu-id="feb2b-120">你还可以使用 `.HasBaseType((Type)null)` 从层次结构中删除实体类型。</span><span class="sxs-lookup"><span data-stu-id="feb2b-120">You can also use `.HasBaseType((Type)null)` to remove an entity type from the hierarchy.</span></span>

## <a name="discriminator-configuration"></a><span data-ttu-id="feb2b-121">鉴别器配置</span><span class="sxs-lookup"><span data-stu-id="feb2b-121">Discriminator configuration</span></span>

<span data-ttu-id="feb2b-122">您可以配置鉴别器列的名称和类型以及用于标识层次结构中的每种类型的值：</span><span class="sxs-lookup"><span data-stu-id="feb2b-122">You can configure the name and type of the discriminator column and the values that are used to identify each type in the hierarchy:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorConfiguration.cs?name=DiscriminatorConfiguration&highlight=4-6)]

<span data-ttu-id="feb2b-123">在上述示例中，EF 在层次结构的基实体上将鉴别器隐式添加为[影子属性](xref:core/modeling/shadow-properties)。</span><span class="sxs-lookup"><span data-stu-id="feb2b-123">In the examples above, EF added the discriminator implicitly as a [shadow property](xref:core/modeling/shadow-properties) on the base entity of the hierarchy.</span></span> <span data-ttu-id="feb2b-124">此属性可以配置为类似于任何其他属性：</span><span class="sxs-lookup"><span data-stu-id="feb2b-124">This property can be configured like any other:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorPropertyConfiguration.cs?name=DiscriminatorPropertyConfiguration&highlight=4-5)]

<span data-ttu-id="feb2b-125">最后，鉴别器还可以映射到实体中的常规 .NET 属性：</span><span class="sxs-lookup"><span data-stu-id="feb2b-125">Finally, the discriminator can also be mapped to a regular .NET property in your entity:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/NonShadowDiscriminator.cs?name=NonShadowDiscriminator&highlight=4)]

## <a name="shared-columns"></a><span data-ttu-id="feb2b-126">共享列</span><span class="sxs-lookup"><span data-stu-id="feb2b-126">Shared columns</span></span>

<span data-ttu-id="feb2b-127">默认情况下，当层次结构中的两个同级实体类型具有相同的属性时，它们将映射到两个单独的列。</span><span class="sxs-lookup"><span data-stu-id="feb2b-127">By default, when two sibling entity types in the hierarchy have a property with the same name, they will be mapped to two separate columns.</span></span> <span data-ttu-id="feb2b-128">但是，如果它们的类型相同，则可以映射到相同的数据库列：</span><span class="sxs-lookup"><span data-stu-id="feb2b-128">However, if their type is identical they can be mapped to the same database column:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedTPHColumns.cs?name=SharedTPHColumns&highlight=9,13)]
