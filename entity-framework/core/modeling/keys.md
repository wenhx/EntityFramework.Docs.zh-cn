---
title: 密钥-EF Core
description: 如何在使用 Entity Framework Core 时配置实体类型的密钥
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/06/2019
uid: core/modeling/keys
ms.openlocfilehash: abd65a5ea079a49fd7a3bbc84a9337f6ee19fab1
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413989"
---
# <a name="keys"></a><span data-ttu-id="70226-103">Keys</span><span class="sxs-lookup"><span data-stu-id="70226-103">Keys</span></span>

<span data-ttu-id="70226-104">键充当每个实体实例的唯一标识符。</span><span class="sxs-lookup"><span data-stu-id="70226-104">A key serves as a unique identifier for each entity instance.</span></span> <span data-ttu-id="70226-105">EF 中的大多数实体都有一个键，此键映射到关系数据库中*主键*的概念（对于没有键的实体，请参阅[无键实体](xref:core/modeling/keyless-entity-types)）。</span><span class="sxs-lookup"><span data-stu-id="70226-105">Most entities in EF have a single key, which maps to the concept of a *primary key* in relational databases (for entities without keys, see [Keyless entities](xref:core/modeling/keyless-entity-types)).</span></span> <span data-ttu-id="70226-106">实体可以有超过主键的其他键（有关详细信息，请参阅[备用键](#alternate-keys)）。</span><span class="sxs-lookup"><span data-stu-id="70226-106">Entities can have additional keys beyond the primary key (see [Alternate Keys](#alternate-keys) for more information).</span></span>

<span data-ttu-id="70226-107">按照约定，将名为 `Id` 或 `<type name>Id` 的属性配置为实体的主键。</span><span class="sxs-lookup"><span data-stu-id="70226-107">By convention, a property named `Id` or `<type name>Id` will be configured as the primary key of an entity.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/KeyId.cs?name=KeyId&highlight=3,11)]

> [!NOTE]
> <span data-ttu-id="70226-108">[拥有的实体类型](xref:core/modeling/owned-entities)使用不同的规则来定义密钥。</span><span class="sxs-lookup"><span data-stu-id="70226-108">[Owned entity types](xref:core/modeling/owned-entities) use different rules to define keys.</span></span>

<span data-ttu-id="70226-109">可以将单个属性配置为实体的主键，如下所示：</span><span class="sxs-lookup"><span data-stu-id="70226-109">You can configure a single property to be the primary key of an entity as follows:</span></span>

## <a name="data-annotations"></a>[<span data-ttu-id="70226-110">数据注释</span><span class="sxs-lookup"><span data-stu-id="70226-110">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/KeySingle.cs?name=KeySingle&highlight=3)]

## <a name="fluent-api"></a>[<span data-ttu-id="70226-111">熟知 API</span><span class="sxs-lookup"><span data-stu-id="70226-111">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeySingle.cs?name=KeySingle&highlight=4)]

***

<span data-ttu-id="70226-112">你还可以将多个属性配置为实体的键，这称为组合键。</span><span class="sxs-lookup"><span data-stu-id="70226-112">You can also configure multiple properties to be the key of an entity - this is known as a composite key.</span></span> <span data-ttu-id="70226-113">复合密钥只能使用熟知的 API 进行配置;约定将永远不会设置组合键，你不能使用数据批注来配置它。</span><span class="sxs-lookup"><span data-stu-id="70226-113">Composite keys can only be configured using the Fluent API; conventions will never setup a composite key, and you can not use Data Annotations to configure one.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyComposite.cs?name=KeyComposite&highlight=4)]

## <a name="primary-key-name"></a><span data-ttu-id="70226-114">主键名称</span><span class="sxs-lookup"><span data-stu-id="70226-114">Primary key name</span></span>

<span data-ttu-id="70226-115">按照约定，使用名称 `PK_<type name>`创建关系数据库主键。</span><span class="sxs-lookup"><span data-stu-id="70226-115">By convention, on relational databases primary keys are created with the name `PK_<type name>`.</span></span> <span data-ttu-id="70226-116">可以按如下所示配置 primary key 约束的名称：</span><span class="sxs-lookup"><span data-stu-id="70226-116">You can configure the name of the primary key constraint as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyName.cs?name=KeyName&highlight=5)]

## <a name="key-types-and-values"></a><span data-ttu-id="70226-117">键类型和值</span><span class="sxs-lookup"><span data-stu-id="70226-117">Key types and values</span></span>

<span data-ttu-id="70226-118">虽然 EF Core 支持使用任何基元类型的属性作为主键，包括 `string`、`Guid`、`byte[]` 和其他类型，但并非所有数据库都支持将所有类型作为键。</span><span class="sxs-lookup"><span data-stu-id="70226-118">While EF Core supports using properties of any primitive type as the primary key, including `string`, `Guid`, `byte[]` and others, not all databases support all types as keys.</span></span> <span data-ttu-id="70226-119">在某些情况下，可以自动将键值转换为支持的类型，否则应[手动指定](xref:core/modeling/value-conversions)转换。</span><span class="sxs-lookup"><span data-stu-id="70226-119">In some cases the key values can be converted to a supported type automatically, otherwise the conversion should be [specified manually](xref:core/modeling/value-conversions).</span></span>

<span data-ttu-id="70226-120">在将新实体添加到上下文时，键属性必须始终具有非默认值，但某些类型将[由数据库生成](xref:core/modeling/generated-properties)。</span><span class="sxs-lookup"><span data-stu-id="70226-120">Key properties must always have a non-default value when adding a new entity to the context, but some types will be [generated by the database](xref:core/modeling/generated-properties).</span></span> <span data-ttu-id="70226-121">在这种情况下，当添加实体进行跟踪时，EF 会尝试生成一个临时值。</span><span class="sxs-lookup"><span data-stu-id="70226-121">In that case EF will try to generate a temporary value when the entity is added for tracking purposes.</span></span> <span data-ttu-id="70226-122">在调用[SaveChanges](/dotnet/api/Microsoft.EntityFrameworkCore.DbContext.SaveChanges)后，临时值将替换为数据库生成的值。</span><span class="sxs-lookup"><span data-stu-id="70226-122">After [SaveChanges](/dotnet/api/Microsoft.EntityFrameworkCore.DbContext.SaveChanges) is called the temporary value will be replaced by the value generated by the database.</span></span>

> [!Important]
> <span data-ttu-id="70226-123">如果某个键属性的值由数据库生成，而在添加实体时指定了一个非默认值，则 EF 将假定该实体在数据库中已存在，并且将尝试对其进行更新而不是插入一个新的值。</span><span class="sxs-lookup"><span data-stu-id="70226-123">If a key property has its value generated by the database and a non-default value is specified when an entity is added, then EF will assume that the entity already exists in the database and will try to update it instead of inserting a new one.</span></span> <span data-ttu-id="70226-124">若要避免这种情况，请禁用值生成或了解[如何为生成的属性指定显式值](../saving/explicit-values-generated-properties.md)。</span><span class="sxs-lookup"><span data-stu-id="70226-124">To avoid this turn off value generation or see [how to specify explicit values for generated properties](../saving/explicit-values-generated-properties.md).</span></span>

## <a name="alternate-keys"></a><span data-ttu-id="70226-125">备用键</span><span class="sxs-lookup"><span data-stu-id="70226-125">Alternate Keys</span></span>

<span data-ttu-id="70226-126">除了主键外，备用键还可用作每个实体实例的替代唯一标识符;它可用作关系的目标。</span><span class="sxs-lookup"><span data-stu-id="70226-126">An alternate key serves as an alternate unique identifier for each entity instance in addition to the primary key; it can be used as the target of a relationship.</span></span> <span data-ttu-id="70226-127">使用关系数据库时，这将映射到备用键列上的唯一索引/约束和引用列的一个或多个外键约束的概念。</span><span class="sxs-lookup"><span data-stu-id="70226-127">When using a relational database this maps to the concept of a unique index/constraint on the alternate key column(s) and one or more foreign key constraints that reference the column(s).</span></span>

> [!TIP]
> <span data-ttu-id="70226-128">如果只是想要在列上强制唯一性，请定义唯一索引而不是备用键（请参阅[索引](indexes.md)）。</span><span class="sxs-lookup"><span data-stu-id="70226-128">If you just want to enforce uniqueness on a column, define a unique index rather than an alternate key (see [Indexes](indexes.md)).</span></span> <span data-ttu-id="70226-129">在 EF 中，备用键为只读，并在唯一索引之上提供附加语义，因为它们可用作外键的目标。</span><span class="sxs-lookup"><span data-stu-id="70226-129">In EF, alternate keys are read-only and provide additional semantics over unique indexes because they can be used as the target of a foreign key.</span></span>

<span data-ttu-id="70226-130">系统通常会在需要时为你引入备用键，你无需手动配置它们。</span><span class="sxs-lookup"><span data-stu-id="70226-130">Alternate keys are typically introduced for you when needed and you do not need to manually configure them.</span></span> <span data-ttu-id="70226-131">按照约定，当您标识的属性不是作为关系目标的主键时，将为您引入备用密钥。</span><span class="sxs-lookup"><span data-stu-id="70226-131">By convention, an alternate key is introduced for you when you identify a property which isn't the primary key as the target of a relationship.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/AlternateKey.cs?name=AlternateKey&highlight=12)]

<span data-ttu-id="70226-132">你还可以将单个属性配置为备用密钥：</span><span class="sxs-lookup"><span data-stu-id="70226-132">You can also configure a single property to be an alternate key:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeySingle.cs?name=AlternateKeySingle&highlight=4)]

<span data-ttu-id="70226-133">你还可以将多个属性配置为备用密钥（称为复合备用密钥）：</span><span class="sxs-lookup"><span data-stu-id="70226-133">You can also configure multiple properties to be an alternate key (known as a composite alternate key):</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeyComposite.cs?name=AlternateKeyComposite&highlight=4)]

<span data-ttu-id="70226-134">最后，按照约定，为备用键引入的索引和约束将被命名为 `AK_<type name>_<property name>` （对于复合备用键 `<property name>` 变成以下划线分隔的属性名称列表）。</span><span class="sxs-lookup"><span data-stu-id="70226-134">Finally, by convention, the index and constraint that are introduced for an alternate key will be named `AK_<type name>_<property name>` (for composite alternate keys `<property name>` becomes an underscore separated list of property names).</span></span> <span data-ttu-id="70226-135">您可以配置备用密钥的 index 和 unique 约束的名称：</span><span class="sxs-lookup"><span data-stu-id="70226-135">You can configure the name of the alternate key's index and unique constraint:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeyName.cs?name=AlternateKeyName&highlight=5)]
