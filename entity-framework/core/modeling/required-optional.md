---
title: 必需/可选属性-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: ddaa0a54-9f43-4c34-aae3-f95c96c69842
uid: core/modeling/required-optional
ms.openlocfilehash: 564d9e62e2ed4f1a52b569630ed4994529e31dc1
ms.sourcegitcommit: 87fcaba46535aa351db4bdb1231bd14b40e459b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2019
ms.locfileid: "59929805"
---
# <a name="required-and-optional-properties"></a><span data-ttu-id="9dcb6-102">必需和可选属性</span><span class="sxs-lookup"><span data-stu-id="9dcb6-102">Required and Optional Properties</span></span>

<span data-ttu-id="9dcb6-103">属性被认为是如果它才能包含有效`null`。</span><span class="sxs-lookup"><span data-stu-id="9dcb6-103">A property is considered optional if it is valid for it to contain `null`.</span></span> <span data-ttu-id="9dcb6-104">如果`null`不是有效的值分配给属性中，则它被视为是必需的属性。</span><span class="sxs-lookup"><span data-stu-id="9dcb6-104">If `null` is not a valid value to be assigned to a property then it is considered to be a required property.</span></span>

## <a name="conventions"></a><span data-ttu-id="9dcb6-105">约定</span><span class="sxs-lookup"><span data-stu-id="9dcb6-105">Conventions</span></span>

<span data-ttu-id="9dcb6-106">按照约定，其 CLR 类型可以包含 null 的属性将配置为可选 (`string`， `int?`， `byte[]`，等等。)。</span><span class="sxs-lookup"><span data-stu-id="9dcb6-106">By convention, a property whose CLR type can contain null will be configured as optional (`string`, `int?`, `byte[]`, etc.).</span></span> <span data-ttu-id="9dcb6-107">将配置的属性的 CLR 类型不能包含 null 所需的方式 (`int`， `decimal`， `bool`，等等。)。</span><span class="sxs-lookup"><span data-stu-id="9dcb6-107">Properties whose CLR type cannot contain null will be configured as required (`int`, `decimal`, `bool`, etc.).</span></span>

> [!NOTE]  
> <span data-ttu-id="9dcb6-108">其 CLR 类型不能包含 null 的属性不能配置为可选。</span><span class="sxs-lookup"><span data-stu-id="9dcb6-108">A property whose CLR type cannot contain null cannot be configured as optional.</span></span> <span data-ttu-id="9dcb6-109">始终将所需的实体框架视为属性。</span><span class="sxs-lookup"><span data-stu-id="9dcb6-109">The property will always be considered required by Entity Framework.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="9dcb6-110">数据注释</span><span class="sxs-lookup"><span data-stu-id="9dcb6-110">Data Annotations</span></span>

<span data-ttu-id="9dcb6-111">可以使用数据批注以指示属性是必需。</span><span class="sxs-lookup"><span data-stu-id="9dcb6-111">You can use Data Annotations to indicate that a property is required.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Required.cs?highlight=14)]

## <a name="fluent-api"></a><span data-ttu-id="9dcb6-112">Fluent API</span><span class="sxs-lookup"><span data-stu-id="9dcb6-112">Fluent API</span></span>

<span data-ttu-id="9dcb6-113">可以使用 Fluent API 以指示属性是必需。</span><span class="sxs-lookup"><span data-stu-id="9dcb6-113">You can use the Fluent API to indicate that a property is required.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Required.cs?highlight=11-13)]

