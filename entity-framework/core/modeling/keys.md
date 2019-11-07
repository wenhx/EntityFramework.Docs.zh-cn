---
title: 密钥（主要）-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 912ffef7-86a0-4cdc-a776-55f907459d20
uid: core/modeling/keys
ms.openlocfilehash: 66c64c389294e8e109a614a2bea8311932660dea
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655946"
---
# <a name="keys-primary"></a><span data-ttu-id="2d2fb-102">键（主要）</span><span class="sxs-lookup"><span data-stu-id="2d2fb-102">Keys (primary)</span></span>

<span data-ttu-id="2d2fb-103">键充当每个实体实例的主唯一标识符。</span><span class="sxs-lookup"><span data-stu-id="2d2fb-103">A key serves as the primary unique identifier for each entity instance.</span></span> <span data-ttu-id="2d2fb-104">使用关系数据库时，这会映射到*主键*的概念。</span><span class="sxs-lookup"><span data-stu-id="2d2fb-104">When using a relational database this maps to the concept of a *primary key*.</span></span> <span data-ttu-id="2d2fb-105">你还可以配置不是主键的唯一标识符（有关详细信息，请参阅[备用键](alternate-keys.md)）。</span><span class="sxs-lookup"><span data-stu-id="2d2fb-105">You can also configure a unique identifier that is not the primary key (see [Alternate Keys](alternate-keys.md) for more information).</span></span>

<span data-ttu-id="2d2fb-106">可以使用以下方法之一来设置/创建主键。</span><span class="sxs-lookup"><span data-stu-id="2d2fb-106">One of the following methods can be used to setup/create a primary key.</span></span>

## <a name="conventions"></a><span data-ttu-id="2d2fb-107">约定</span><span class="sxs-lookup"><span data-stu-id="2d2fb-107">Conventions</span></span>

<span data-ttu-id="2d2fb-108">按照约定，将名为 `Id` 或 `<type name>Id` 的属性配置为实体的键。</span><span class="sxs-lookup"><span data-stu-id="2d2fb-108">By convention, a property named `Id` or `<type name>Id` will be configured as the key of an entity.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/KeyId.cs?name=KeyId&highlight=3)]

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/KeyTypeNameId.cs?name=KeyIdhighlight=3)]

## <a name="data-annotations"></a><span data-ttu-id="2d2fb-109">数据注释</span><span class="sxs-lookup"><span data-stu-id="2d2fb-109">Data Annotations</span></span>

<span data-ttu-id="2d2fb-110">您可以使用数据批注将单个属性配置为实体的键。</span><span class="sxs-lookup"><span data-stu-id="2d2fb-110">You can use Data Annotations to configure a single property to be the key of an entity.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/KeySingle.cs?highlight=13)]

## <a name="fluent-api"></a><span data-ttu-id="2d2fb-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="2d2fb-111">Fluent API</span></span>

<span data-ttu-id="2d2fb-112">您可以使用熟知的 API 将单个属性配置为实体的键。</span><span class="sxs-lookup"><span data-stu-id="2d2fb-112">You can use the Fluent API to configure a single property to be the key of an entity.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeySingle.cs?highlight=11,12)]

<span data-ttu-id="2d2fb-113">你还可以使用熟知的 API 将多个属性配置为实体的密钥（称为组合键）。</span><span class="sxs-lookup"><span data-stu-id="2d2fb-113">You can also use the Fluent API to configure multiple properties to be the key of an entity (known as a composite key).</span></span> <span data-ttu-id="2d2fb-114">只能使用熟知的 API 约定来配置复合密钥，而不会设置组合键，也不能使用数据批注来配置复合密钥。</span><span class="sxs-lookup"><span data-stu-id="2d2fb-114">Composite keys can only be configured using the Fluent API - conventions will never setup a composite key and you can not use Data Annotations to configure one.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyComposite.cs?highlight=11,12)]
