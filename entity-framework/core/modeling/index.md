---
title: 创建并配置模型 - EF Core
description: 使用 Entity Framework Core 创建并配置模型的概述
author: AndriySvyryd
ms.date: 11/05/2019
uid: core/modeling/index
ms.openlocfilehash: 7f8ef17f33a294dc0b8cc9c4e514a8a29b761342
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063057"
---
# <a name="creating-and-configuring-a-model"></a><span data-ttu-id="a3ad5-103">创建并配置模型</span><span class="sxs-lookup"><span data-stu-id="a3ad5-103">Creating and configuring a model</span></span>

<span data-ttu-id="a3ad5-104">Entity Framework 使用一组约定基于实体类的形状构建模型。</span><span class="sxs-lookup"><span data-stu-id="a3ad5-104">Entity Framework uses a set of conventions to build a model based on the shape of your entity classes.</span></span> <span data-ttu-id="a3ad5-105">可指定其他配置以补充和/或替代约定的内容。</span><span class="sxs-lookup"><span data-stu-id="a3ad5-105">You can specify additional configuration to supplement and/or override what was discovered by convention.</span></span>

<span data-ttu-id="a3ad5-106">本文介绍可应用于面向任何数据存储的模型的配置，以及面向任意关系数据库时可应用的配置。</span><span class="sxs-lookup"><span data-stu-id="a3ad5-106">This article covers configuration that can be applied to a model targeting any data store and that which can be applied when targeting any relational database.</span></span> <span data-ttu-id="a3ad5-107">提供程序还可支持特定于具体数据存储的配置。</span><span class="sxs-lookup"><span data-stu-id="a3ad5-107">Providers may also enable configuration that is specific to a particular data store.</span></span> <span data-ttu-id="a3ad5-108">有关提供程序特定配置的文档，请参阅 [数据库提供程序](xref:core/providers/index) 部分。</span><span class="sxs-lookup"><span data-stu-id="a3ad5-108">For documentation on provider specific configuration see the [Database Providers](xref:core/providers/index) section.</span></span>

> [!TIP]  
> <span data-ttu-id="a3ad5-109">可在 GitHub 上查看此文章的 [示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples) 。</span><span class="sxs-lookup"><span data-stu-id="a3ad5-109">You can view this article’s [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples) on GitHub.</span></span>

## <a name="use-fluent-api-to-configure-a-model"></a><span data-ttu-id="a3ad5-110">使用 fluent API 配置模型</span><span class="sxs-lookup"><span data-stu-id="a3ad5-110">Use fluent API to configure a model</span></span>

<span data-ttu-id="a3ad5-111">可在派生上下文中覆写 `OnModelCreating` 方法，并使用 `ModelBuilder API` 来配置模型。</span><span class="sxs-lookup"><span data-stu-id="a3ad5-111">You can override the `OnModelCreating` method in your derived context and use the `ModelBuilder API` to configure your model.</span></span> <span data-ttu-id="a3ad5-112">此配置方法最为有效，并可在不修改实体类的情况下指定配置。</span><span class="sxs-lookup"><span data-stu-id="a3ad5-112">This is the most powerful method of configuration and allows configuration to be specified without modifying your entity classes.</span></span> <span data-ttu-id="a3ad5-113">Fluent API 配置具有最高优先级，并将替代约定和数据注释。</span><span class="sxs-lookup"><span data-stu-id="a3ad5-113">Fluent API configuration has the highest precedence and will override conventions and data annotations.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?highlight=12-14)]

## <a name="use-data-annotations-to-configure-a-model"></a><span data-ttu-id="a3ad5-114">使用数据注释来配置模型</span><span class="sxs-lookup"><span data-stu-id="a3ad5-114">Use data annotations to configure a model</span></span>

<span data-ttu-id="a3ad5-115">也可将特性（称为数据注释）应用于类和属性。</span><span class="sxs-lookup"><span data-stu-id="a3ad5-115">You can also apply attributes (known as Data Annotations) to your classes and properties.</span></span> <span data-ttu-id="a3ad5-116">数据注释会替代约定，但会被 Fluent API 配置替代。</span><span class="sxs-lookup"><span data-stu-id="a3ad5-116">Data annotations will override conventions, but will be overridden by Fluent API configuration.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?highlight=15)]
