---
title: EF Core 中的主键
description: 如何在使用 Entity Framework Core 时配置实体类型的密钥
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/06/2019
uid: core/modeling/keys
ms.openlocfilehash: fdaccb42259ba9dad97a05c626edd0291ca96cb0
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824620"
---
# <a name="keys-primary"></a><span data-ttu-id="4cc4e-103">键（主要）</span><span class="sxs-lookup"><span data-stu-id="4cc4e-103">Keys (primary)</span></span>

<span data-ttu-id="4cc4e-104">键用作每个实体实例的主要唯一标识符。</span><span class="sxs-lookup"><span data-stu-id="4cc4e-104">A key serves as the primary unique identifier for each entity instance.</span></span> <span data-ttu-id="4cc4e-105">使用关系数据库时，这会映射到主键的概念。</span><span class="sxs-lookup"><span data-stu-id="4cc4e-105">When using a relational database this maps to the concept of a *primary key*.</span></span> <span data-ttu-id="4cc4e-106">您还可以配置不是主键的唯一标识符（有关详细信息，请参阅[备用键](alternate-keys.md)</span><span class="sxs-lookup"><span data-stu-id="4cc4e-106">You can also configure a unique identifier that is not the primary key (see [Alternate Keys](alternate-keys.md) for more information).</span></span>

<span data-ttu-id="4cc4e-107">可以使用以下方法之一来设置/创建主键。</span><span class="sxs-lookup"><span data-stu-id="4cc4e-107">One of the following methods can be used to setup/create a primary key.</span></span>

## <a name="conventions"></a><span data-ttu-id="4cc4e-108">约定</span><span class="sxs-lookup"><span data-stu-id="4cc4e-108">Conventions</span></span>

<span data-ttu-id="4cc4e-109">默认情况下，将名为 `Id` 或 `<type name>Id` 的属性配置为实体的键。</span><span class="sxs-lookup"><span data-stu-id="4cc4e-109">By default, a property named `Id` or `<type name>Id` will be configured as the key of an entity.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/KeyId.cs?name=KeyId&highlight=3)]

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/KeyTypeNameId.cs?name=KeyId&highlight=3)]

> [!NOTE]
> <span data-ttu-id="4cc4e-110">[拥有的实体类型](xref:core/modeling/owned-entities)使用不同的规则来定义密钥。</span><span class="sxs-lookup"><span data-stu-id="4cc4e-110">[Owned entity types](xref:core/modeling/owned-entities) use different rules to define keys.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="4cc4e-111">数据注释</span><span class="sxs-lookup"><span data-stu-id="4cc4e-111">Data Annotations</span></span>

<span data-ttu-id="4cc4e-112">可以使用数据注释将单个属性配置为实体的键。</span><span class="sxs-lookup"><span data-stu-id="4cc4e-112">You can use Data Annotations to configure a single property to be the key of an entity.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/KeySingle.cs?highlight=13)]

## <a name="fluent-api"></a><span data-ttu-id="4cc4e-113">Fluent API</span><span class="sxs-lookup"><span data-stu-id="4cc4e-113">Fluent API</span></span>

<span data-ttu-id="4cc4e-114">可以使用 Fluent API 将单个属性配置为实体的键。</span><span class="sxs-lookup"><span data-stu-id="4cc4e-114">You can use the Fluent API to configure a single property to be the key of an entity.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeySingle.cs?highlight=11,12)]

<span data-ttu-id="4cc4e-115">Fluent API 还可用于将多个属性配置为实体的键（称为复合键）。</span><span class="sxs-lookup"><span data-stu-id="4cc4e-115">You can also use the Fluent API to configure multiple properties to be the key of an entity (known as a composite key).</span></span> <span data-ttu-id="4cc4e-116">只能使用 Fluent API 配置复合键 - 不能使用约定来设置复合键，也不能使用数据注释来配置复合键。</span><span class="sxs-lookup"><span data-stu-id="4cc4e-116">Composite keys can only be configured using the Fluent API - conventions will never setup a composite key and you can not use Data Annotations to configure one.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyComposite.cs?highlight=11,12)]

## <a name="key-types-and-values"></a><span data-ttu-id="4cc4e-117">键类型和值</span><span class="sxs-lookup"><span data-stu-id="4cc4e-117">Key types and values</span></span>

<span data-ttu-id="4cc4e-118">EF Core 支持使用任何基元类型的属性作为主键，包括 `string`、`Guid`、`byte[]` 和其他项。</span><span class="sxs-lookup"><span data-stu-id="4cc4e-118">EF Core supports using properties of any primitive type as the primary key, including `string`, `Guid`, `byte[]` and others.</span></span> <span data-ttu-id="4cc4e-119">但并非所有数据库都支持它们。</span><span class="sxs-lookup"><span data-stu-id="4cc4e-119">But not all databases support them.</span></span> <span data-ttu-id="4cc4e-120">在某些情况下，可以自动将键值转换为支持的类型，否则应[手动指定](xref:core/modeling/value-conversions)转换。</span><span class="sxs-lookup"><span data-stu-id="4cc4e-120">In some cases the key values can be converted to a supported type automatically, otherwise the conversion should be [specified manually](xref:core/modeling/value-conversions).</span></span>

<span data-ttu-id="4cc4e-121">在将新实体添加到上下文时，键属性必须始终具有非默认值，但某些类型将[由数据库生成](xref:core/modeling/generated-properties)。</span><span class="sxs-lookup"><span data-stu-id="4cc4e-121">Key properties must always have a non-default value when adding a new entity to the context, but some types will be [generated by the database](xref:core/modeling/generated-properties).</span></span> <span data-ttu-id="4cc4e-122">在这种情况下，当添加实体进行跟踪时，EF 会尝试生成一个临时值。</span><span class="sxs-lookup"><span data-stu-id="4cc4e-122">In that case EF will try to generate a temporary value when the entity is added for tracking purposes.</span></span> <span data-ttu-id="4cc4e-123">在调用[SaveChanges](/dotnet/api/Microsoft.EntityFrameworkCore.DbContext.SaveChanges)后，临时值将替换为数据库生成的值。</span><span class="sxs-lookup"><span data-stu-id="4cc4e-123">After [SaveChanges](/dotnet/api/Microsoft.EntityFrameworkCore.DbContext.SaveChanges) is called the temporary value will be replaced by the value generated by the database.</span></span>

> [!Important]
> <span data-ttu-id="4cc4e-124">如果某个键属性具有数据库生成的值，并且在添加实体时指定了一个非默认值，则 EF 将假定该实体在数据库中已存在，并且将尝试进行更新，而不是插入一个新的值。</span><span class="sxs-lookup"><span data-stu-id="4cc4e-124">If a key property has value generated by the database and a non-default value is specified when an entity is added then EF will assume that the entity already exists in the database and will try to update it instead of inserting a new one.</span></span> <span data-ttu-id="4cc4e-125">若要避免这种情况，请禁用值生成或了解[如何为生成的属性指定显式值](../saving/explicit-values-generated-properties.md)。</span><span class="sxs-lookup"><span data-stu-id="4cc4e-125">To avoid this turn off value generation or see [how to specify explicit values for generated properties](../saving/explicit-values-generated-properties.md).</span></span>