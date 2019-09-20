---
title: 必需/可选属性-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: ddaa0a54-9f43-4c34-aae3-f95c96c69842
uid: core/modeling/required-optional
ms.openlocfilehash: 7200cd2eeeba2f22365ef09b1f50edd077240130
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149145"
---
# <a name="required-and-optional-properties"></a><span data-ttu-id="e4871-102">必需属性和可选属性</span><span class="sxs-lookup"><span data-stu-id="e4871-102">Required and Optional Properties</span></span>

<span data-ttu-id="e4871-103">如果属性对于包含`null`有效，则将其视为可选。</span><span class="sxs-lookup"><span data-stu-id="e4871-103">A property is considered optional if it is valid for it to contain `null`.</span></span> <span data-ttu-id="e4871-104">如果`null`不是要分配给属性的有效值，则将其视为必需属性。</span><span class="sxs-lookup"><span data-stu-id="e4871-104">If `null` is not a valid value to be assigned to a property then it is considered to be a required property.</span></span>

## <a name="conventions"></a><span data-ttu-id="e4871-105">约定</span><span class="sxs-lookup"><span data-stu-id="e4871-105">Conventions</span></span>

<span data-ttu-id="e4871-106">按照约定，.net 类型可以包含 null 值的属性将配置为可选（`string`、 `int?`、 `byte[]`等）。</span><span class="sxs-lookup"><span data-stu-id="e4871-106">By convention, a property whose .NET type can contain null will be configured as optional (`string`, `int?`, `byte[]`, etc.).</span></span> <span data-ttu-id="e4871-107">将配置其 CLR 类型不能包含 null 的属性（`int`、 `decimal`、 `bool`等）。</span><span class="sxs-lookup"><span data-stu-id="e4871-107">Properties whose CLR type cannot contain null will be configured as required (`int`, `decimal`, `bool`, etc.).</span></span>

> [!NOTE]  
> <span data-ttu-id="e4871-108">不能将其 .NET 类型不能包含 null 的属性配置为可选。</span><span class="sxs-lookup"><span data-stu-id="e4871-108">A property whose .NET type cannot contain null cannot be configured as optional.</span></span> <span data-ttu-id="e4871-109">实体框架，将始终认为属性是必需的。</span><span class="sxs-lookup"><span data-stu-id="e4871-109">The property will always be considered required by Entity Framework.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="e4871-110">数据注释</span><span class="sxs-lookup"><span data-stu-id="e4871-110">Data Annotations</span></span>

<span data-ttu-id="e4871-111">您可以使用数据批注来指示属性是必需的。</span><span class="sxs-lookup"><span data-stu-id="e4871-111">You can use Data Annotations to indicate that a property is required.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Required.cs?highlight=14)]

## <a name="fluent-api"></a><span data-ttu-id="e4871-112">Fluent API</span><span class="sxs-lookup"><span data-stu-id="e4871-112">Fluent API</span></span>

<span data-ttu-id="e4871-113">您可以使用 "熟知 API" 来指示属性是必需的。</span><span class="sxs-lookup"><span data-stu-id="e4871-113">You can use the Fluent API to indicate that a property is required.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Required.cs?highlight=11-13)]

