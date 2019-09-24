---
title: 包括和排除属性 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e9dff604-3469-4a05-8f9e-18ac281d82a9
uid: core/modeling/included-properties
ms.openlocfilehash: cd111af891ef0bbaccf515eed0c1991f105bd362
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197421"
---
# <a name="including--excluding-properties"></a><span data-ttu-id="b657b-102">包含属性和排除属性</span><span class="sxs-lookup"><span data-stu-id="b657b-102">Including & Excluding Properties</span></span>

<span data-ttu-id="b657b-103">模型中包含一个属性意味着 EF 拥有该属性的元数据，并会尝试从数据库读取值或者向数据库写入值。</span><span class="sxs-lookup"><span data-stu-id="b657b-103">Including a property in the model means that EF has metadata about that property and will attempt to read and write values from/to the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="b657b-104">约定</span><span class="sxs-lookup"><span data-stu-id="b657b-104">Conventions</span></span>

<span data-ttu-id="b657b-105">按照约定，模型所含的那些公共属性都拥有一个 getter 和一个 setter。</span><span class="sxs-lookup"><span data-stu-id="b657b-105">By convention, public properties with a getter and a setter will be included in the model.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="b657b-106">数据注释</span><span class="sxs-lookup"><span data-stu-id="b657b-106">Data Annotations</span></span>

<span data-ttu-id="b657b-107">您可以使用数据注释方式来从模型中排除某个属性。</span><span class="sxs-lookup"><span data-stu-id="b657b-107">You can use Data Annotations to exclude a property from the model.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreProperty.cs?highlight=17)]

## <a name="fluent-api"></a><span data-ttu-id="b657b-108">Fluent API</span><span class="sxs-lookup"><span data-stu-id="b657b-108">Fluent API</span></span>

<span data-ttu-id="b657b-109">您也可以用Fluent API 从模型中排除某个属性。</span><span class="sxs-lookup"><span data-stu-id="b657b-109">You can use the Fluent API to exclude a property from the model.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreProperty.cs?highlight=12,13)]
