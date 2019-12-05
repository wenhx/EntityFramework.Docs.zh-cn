---
title: 继承 （关系数据库） 的 EF Core
description: 如何使用 Entity Framework Core 配置关系数据库中的实体类型继承
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/06/2019
uid: core/modeling/relational/inheritance
ms.openlocfilehash: 30e25aa2968ceab03404baddb46e0ae59fc3ea6b
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824753"
---
# <a name="inheritance-relational-database"></a><span data-ttu-id="ca4c3-103">继承（关系数据库）</span><span class="sxs-lookup"><span data-stu-id="ca4c3-103">Inheritance (Relational Database)</span></span>

> [!NOTE]  
> <span data-ttu-id="ca4c3-104">一般而言，本部分中的配置适用于关系数据库。</span><span class="sxs-lookup"><span data-stu-id="ca4c3-104">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="ca4c3-105">安装关系数据库提供程序时，此处显示的扩展方法将变为可用（原因在于共享的 Microsoft.EntityFrameworkCore.Relational 包）。</span><span class="sxs-lookup"><span data-stu-id="ca4c3-105">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="ca4c3-106">EF 模型中的继承用于控制如何在数据库中表示实体类中的继承。</span><span class="sxs-lookup"><span data-stu-id="ca4c3-106">Inheritance in the EF model is used to control how inheritance in the entity classes is represented in the database.</span></span>

> [!NOTE]  
> <span data-ttu-id="ca4c3-107">目前，每个层次结构一个表的 (TPH) 模式在 EF Core 实现。</span><span class="sxs-lookup"><span data-stu-id="ca4c3-107">Currently, only the table-per-hierarchy (TPH) pattern is implemented in EF Core.</span></span> <span data-ttu-id="ca4c3-108">其他常见模式（例如每种类型一个表（TPT））和每个具体的表类型（TPC）尚不可用。</span><span class="sxs-lookup"><span data-stu-id="ca4c3-108">Other common patterns like table-per-type (TPT) and table-per-concrete-type (TPC) are not yet available.</span></span>

## <a name="conventions"></a><span data-ttu-id="ca4c3-109">约定</span><span class="sxs-lookup"><span data-stu-id="ca4c3-109">Conventions</span></span>

<span data-ttu-id="ca4c3-110">默认情况下，将使用每个层次结构一个表（TPH）模式映射继承。</span><span class="sxs-lookup"><span data-stu-id="ca4c3-110">By default, inheritance will be mapped using the table-per-hierarchy (TPH) pattern.</span></span> <span data-ttu-id="ca4c3-111">TPH 使用单个表来存储层次结构中所有类型的数据。</span><span class="sxs-lookup"><span data-stu-id="ca4c3-111">TPH uses a single table to store the data for all types in the hierarchy.</span></span> <span data-ttu-id="ca4c3-112">鉴别器列用于标识每行所表示的类型。</span><span class="sxs-lookup"><span data-stu-id="ca4c3-112">A discriminator column is used to identify which type each row represents.</span></span>

<span data-ttu-id="ca4c3-113">在模型中显式包含两个或多个继承的类型时，EF Core 将仅安装程序继承 (请参阅[继承](../inheritance.md)有关详细信息)。</span><span class="sxs-lookup"><span data-stu-id="ca4c3-113">EF Core will only setup inheritance if two or more inherited types are explicitly included in the model (see [Inheritance](../inheritance.md) for more details).</span></span>

<span data-ttu-id="ca4c3-114">下面的示例演示了一个简单的继承方案，以及使用 TPH 模式存储在关系数据库表中的数据。</span><span class="sxs-lookup"><span data-stu-id="ca4c3-114">Below is an example showing a simple inheritance scenario and the data stored in a relational database table using the TPH pattern.</span></span> <span data-ttu-id="ca4c3-115">*鉴别*器列标识每个行中存储哪种类型的*博客*。</span><span class="sxs-lookup"><span data-stu-id="ca4c3-115">The *Discriminator* column identifies which type of *Blog* is stored in each row.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs#Model)]

![图像](_static/inheritance-tph-data.png)

>[!NOTE]
> <span data-ttu-id="ca4c3-117">使用 TPH 映射时，数据库列会根据需要自动进行为 null。</span><span class="sxs-lookup"><span data-stu-id="ca4c3-117">Database columns are automatically made nullable as necessary when using TPH mapping.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="ca4c3-118">数据注释</span><span class="sxs-lookup"><span data-stu-id="ca4c3-118">Data Annotations</span></span>

<span data-ttu-id="ca4c3-119">不能使用数据批注来配置继承。</span><span class="sxs-lookup"><span data-stu-id="ca4c3-119">You cannot use Data Annotations to configure inheritance.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="ca4c3-120">Fluent API</span><span class="sxs-lookup"><span data-stu-id="ca4c3-120">Fluent API</span></span>

<span data-ttu-id="ca4c3-121">您可以使用熟知的 API 来配置鉴别器列的名称和类型，以及用于标识层次结构中的每个类型的值。</span><span class="sxs-lookup"><span data-stu-id="ca4c3-121">You can use the Fluent API to configure the name and type of the discriminator column and the values that are used to identify each type in the hierarchy.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/InheritanceTPHDiscriminator.cs#Inheritance)]

## <a name="configuring-the-discriminator-property"></a><span data-ttu-id="ca4c3-122">配置鉴别器属性</span><span class="sxs-lookup"><span data-stu-id="ca4c3-122">Configuring the discriminator property</span></span>

<span data-ttu-id="ca4c3-123">在上面的示例中，将在层次结构的基实体上将鉴别器创建为[影子属性](xref:core/modeling/shadow-properties)。</span><span class="sxs-lookup"><span data-stu-id="ca4c3-123">In the examples above, the discriminator is created as a [shadow property](xref:core/modeling/shadow-properties) on the base entity of the hierarchy.</span></span> <span data-ttu-id="ca4c3-124">由于它是模型中的属性，因此可以像配置其他属性一样对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="ca4c3-124">Since it is a property in the model, it can be configured just like other properties.</span></span> <span data-ttu-id="ca4c3-125">例如，若要设置默认的、按约定的鉴别器正在使用的最大长度，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="ca4c3-125">For example, to set the max length when the default, by-convention discriminator is being used:</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/DefaultDiscriminator.cs#DiscriminatorConfiguration)]

<span data-ttu-id="ca4c3-126">鉴别器还可以映射到实体中的 .NET 属性并对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="ca4c3-126">The discriminator can also be mapped to a .NET property in your entity and configure it.</span></span> <span data-ttu-id="ca4c3-127">例如：</span><span class="sxs-lookup"><span data-stu-id="ca4c3-127">For example:</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/NonShadowDiscriminator.cs#NonShadowDiscriminator)]

## <a name="shared-columns"></a><span data-ttu-id="ca4c3-128">共享列</span><span class="sxs-lookup"><span data-stu-id="ca4c3-128">Shared columns</span></span>

<span data-ttu-id="ca4c3-129">如果两个同级实体类型具有同名属性，则默认情况下，它们将映射到两个不同的列。</span><span class="sxs-lookup"><span data-stu-id="ca4c3-129">When two sibling entity types have a property with the same name they will be mapped to two separate columns by default.</span></span> <span data-ttu-id="ca4c3-130">但是，如果它们兼容，则可以映射到同一列：</span><span class="sxs-lookup"><span data-stu-id="ca4c3-130">But if they are compatible they can be mapped to the same column:</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/SharedTPHColumns.cs#SharedTPHColumns)]