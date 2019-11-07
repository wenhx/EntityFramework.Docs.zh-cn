---
title: 备用键-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 8a5931d4-b480-4298-af36-0e29d74a37c0
uid: core/modeling/alternate-keys
ms.openlocfilehash: e5bb0602adb465435c8e88d045395a9424b2d9a3
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655775"
---
# <a name="alternate-keys"></a><span data-ttu-id="470a8-102">备用键</span><span class="sxs-lookup"><span data-stu-id="470a8-102">Alternate Keys</span></span>

<span data-ttu-id="470a8-103">除了主键外，备用键还可用作每个实体实例的替代唯一标识符。</span><span class="sxs-lookup"><span data-stu-id="470a8-103">An alternate key serves as an alternate unique identifier for each entity instance in addition to the primary key.</span></span> <span data-ttu-id="470a8-104">备用键可用作关系的目标。</span><span class="sxs-lookup"><span data-stu-id="470a8-104">Alternate keys can be used as the target of a relationship.</span></span> <span data-ttu-id="470a8-105">在使用关系数据库时，这会映射到替换键列上的唯一索引/约束的概念，以及引用列的一个或多个 foreign key 约束。</span><span class="sxs-lookup"><span data-stu-id="470a8-105">When using a relational database this maps to the concept of a unique index/constraint on the alternate key column(s) and one or more foreign key constraints that reference the column(s).</span></span>

> [!TIP]  
> <span data-ttu-id="470a8-106">如果只想强制列的唯一性，则需要唯一索引而不是备用键，请参阅[索引](indexes.md)。</span><span class="sxs-lookup"><span data-stu-id="470a8-106">If you just want to enforce uniqueness of a column then you want a unique index rather than an alternate key, see [Indexes](indexes.md).</span></span> <span data-ttu-id="470a8-107">在 EF 中，除唯一索引外，替代键提供的功能更多，因为它们可用作外键的目标。</span><span class="sxs-lookup"><span data-stu-id="470a8-107">In EF, alternate keys provide greater functionality than unique indexes because they can be used as the target of a foreign key.</span></span>

<span data-ttu-id="470a8-108">通常会在需要时为您引入备用密钥，而不需要手动配置。</span><span class="sxs-lookup"><span data-stu-id="470a8-108">Alternate keys are typically introduced for you when needed and you do not need to manually configure them.</span></span> <span data-ttu-id="470a8-109">有关更多详细信息，请参阅[约定](#conventions)。</span><span class="sxs-lookup"><span data-stu-id="470a8-109">See [Conventions](#conventions) for more details.</span></span>

## <a name="conventions"></a><span data-ttu-id="470a8-110">约定</span><span class="sxs-lookup"><span data-stu-id="470a8-110">Conventions</span></span>

<span data-ttu-id="470a8-111">按照约定，当你将不是主键的属性标识为关系的目标时，会为你引入备用密钥。</span><span class="sxs-lookup"><span data-stu-id="470a8-111">By convention, an alternate key is introduced for you when you identify a property, that is not the primary key, as the target of a relationship.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/AlternateKey.cs?name=AlternateKey&highlight=12)]

## <a name="data-annotations"></a><span data-ttu-id="470a8-112">数据注释</span><span class="sxs-lookup"><span data-stu-id="470a8-112">Data Annotations</span></span>

<span data-ttu-id="470a8-113">不能使用数据批注配置替代键。</span><span class="sxs-lookup"><span data-stu-id="470a8-113">Alternate keys can not be configured using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="470a8-114">Fluent API</span><span class="sxs-lookup"><span data-stu-id="470a8-114">Fluent API</span></span>

<span data-ttu-id="470a8-115">您可以使用熟知的 API 将单个属性配置为备用密钥。</span><span class="sxs-lookup"><span data-stu-id="470a8-115">You can use the Fluent API to configure a single property to be an alternate key.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeySingle.cs?name=AlternateKeySingle&highlight=7,8)]

<span data-ttu-id="470a8-116">你还可以使用熟知的 API 将多个属性配置为备用密钥（称为复合备用键）。</span><span class="sxs-lookup"><span data-stu-id="470a8-116">You can also use the Fluent API to configure multiple properties to be an alternate key (known as a composite alternate key).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeyComposite.cs?name=AlternateKeyComposite&highlight=7,8)]
