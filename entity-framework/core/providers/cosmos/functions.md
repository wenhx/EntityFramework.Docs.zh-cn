---
title: 函数映射-Azure Cosmos DB 提供程序-EF Core
description: Azure Cosmos DB EF Core 提供程序的函数映射
author: bricelam
ms.date: 10/08/2020
uid: core/providers/cosmos/functions
ms.openlocfilehash: ffcf82fbe57ea16e6fc62f3c9c80c572b3e1ed91
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066505"
---
# <a name="function-mappings-of-the-azure-cosmos-db-ef-core-provider"></a><span data-ttu-id="52297-103">Azure Cosmos DB EF Core 提供程序的函数映射</span><span class="sxs-lookup"><span data-stu-id="52297-103">Function Mappings of the Azure Cosmos DB EF Core Provider</span></span>

<span data-ttu-id="52297-104">此页显示使用 Azure Cosmos DB 提供程序时，哪些 .NET 成员转换为哪些 SQL 函数。</span><span class="sxs-lookup"><span data-stu-id="52297-104">This page shows which .NET members are translated into which SQL functions when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="52297-105">.NET</span><span class="sxs-lookup"><span data-stu-id="52297-105">.NET</span></span>                          | <span data-ttu-id="52297-106">SQL</span><span class="sxs-lookup"><span data-stu-id="52297-106">SQL</span></span>                              | <span data-ttu-id="52297-107">在</span><span class="sxs-lookup"><span data-stu-id="52297-107">Added in</span></span>
----------------------------- | -------------------------------- | --------
<span data-ttu-id="52297-108">集合.包含 (项) </span><span class="sxs-lookup"><span data-stu-id="52297-108">collection.Contains(item)</span></span>     | <span data-ttu-id="52297-109">@item 中 @collection</span><span class="sxs-lookup"><span data-stu-id="52297-109">@item IN @collection</span></span>
<span data-ttu-id="52297-110">stringValue 包含 (值) </span><span class="sxs-lookup"><span data-stu-id="52297-110">stringValue.Contains(value)</span></span>   | <span data-ttu-id="52297-111">包含 (@stringValue 、 @value) </span><span class="sxs-lookup"><span data-stu-id="52297-111">CONTAINS(@stringValue, @value)</span></span>   | <span data-ttu-id="52297-112">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="52297-112">EF Core 5.0</span></span>
<span data-ttu-id="52297-113">stringValue. EndsWith (值) </span><span class="sxs-lookup"><span data-stu-id="52297-113">stringValue.EndsWith(value)</span></span>   | <span data-ttu-id="52297-114">ENDSWITH (@stringValue ， @value) </span><span class="sxs-lookup"><span data-stu-id="52297-114">ENDSWITH(@stringValue, @value)</span></span>   | <span data-ttu-id="52297-115">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="52297-115">EF Core 5.0</span></span>
<span data-ttu-id="52297-116">stringValue. FirstOrDefault ( # A1</span><span class="sxs-lookup"><span data-stu-id="52297-116">stringValue.FirstOrDefault()</span></span>  | <span data-ttu-id="52297-117">左 (@stringValue ，1) </span><span class="sxs-lookup"><span data-stu-id="52297-117">LEFT(@stringValue, 1)</span></span>            | <span data-ttu-id="52297-118">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="52297-118">EF Core 5.0</span></span>
<span data-ttu-id="52297-119">stringValue. LastOrDefault ( # A1</span><span class="sxs-lookup"><span data-stu-id="52297-119">stringValue.LastOrDefault()</span></span>   | <span data-ttu-id="52297-120">RIGHT (@stringValue ，1) </span><span class="sxs-lookup"><span data-stu-id="52297-120">RIGHT(@stringValue, 1)</span></span>           | <span data-ttu-id="52297-121">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="52297-121">EF Core 5.0</span></span>
<span data-ttu-id="52297-122">stringValue. StartsWith (值) </span><span class="sxs-lookup"><span data-stu-id="52297-122">stringValue.StartsWith(value)</span></span> | <span data-ttu-id="52297-123">STARTSWITH (@stringValue ， @value) </span><span class="sxs-lookup"><span data-stu-id="52297-123">STARTSWITH(@stringValue, @value)</span></span> | <span data-ttu-id="52297-124">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="52297-124">EF Core 5.0</span></span>
