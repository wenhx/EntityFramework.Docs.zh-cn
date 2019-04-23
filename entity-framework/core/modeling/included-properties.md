---
title: 包括和排除属性 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e9dff604-3469-4a05-8f9e-18ac281d82a9
uid: core/modeling/included-properties
ms.openlocfilehash: 022534091bb48e491c8808791a401216a339d7b0
ms.sourcegitcommit: 87fcaba46535aa351db4bdb1231bd14b40e459b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2019
ms.locfileid: "59929818"
---
# <a name="including--excluding-properties"></a><span data-ttu-id="70a94-102">包括和排除属性</span><span class="sxs-lookup"><span data-stu-id="70a94-102">Including & Excluding Properties</span></span>

<span data-ttu-id="70a94-103">模型中包含属性意味着 EF 拥有该属性的元数据，并将尝试从数据库读取值或者向数据库写入值。</span><span class="sxs-lookup"><span data-stu-id="70a94-103">Including a property in the model means that EF has metadata about that property and will attempt to read and write values from/to the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="70a94-104">约定</span><span class="sxs-lookup"><span data-stu-id="70a94-104">Conventions</span></span>

<span data-ttu-id="70a94-105">按照约定，模型所含的那些公共属性都拥有一个 getter 和一个 setter。</span><span class="sxs-lookup"><span data-stu-id="70a94-105">By convention, public properties with a getter and a setter will be included in the model.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="70a94-106">数据注释</span><span class="sxs-lookup"><span data-stu-id="70a94-106">Data Annotations</span></span>

<span data-ttu-id="70a94-107">您可以使用数据注释方式来从模型中排除某个属性。</span><span class="sxs-lookup"><span data-stu-id="70a94-107">You can use Data Annotations to exclude a property from the model.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/IgnoreProperty.cs?highlight=17)]

## <a name="fluent-api"></a><span data-ttu-id="70a94-108">Fluent API</span><span class="sxs-lookup"><span data-stu-id="70a94-108">Fluent API</span></span>

<span data-ttu-id="70a94-109">您也可以用Fluent API 从模型中排除某个属性。</span><span class="sxs-lookup"><span data-stu-id="70a94-109">You can use the Fluent API to exclude a property from the model.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/IgnoreProperty.cs?highlight=12,13)]
