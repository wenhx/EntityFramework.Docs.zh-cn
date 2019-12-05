---
title: 创建并配置模型 - EF Core
author: rowanmiller
ms.date: 11/05/2019
ms.assetid: 88253ff3-174e-485c-b3f8-768243d01ee1
uid: core/modeling/index
ms.openlocfilehash: 58be4a45473c6292790da341e360b3340de27be7
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824696"
---
# <a name="creating-and-configuring-a-model"></a><span data-ttu-id="bd516-102">创建并配置模型</span><span class="sxs-lookup"><span data-stu-id="bd516-102">Creating and configuring a model</span></span>

<span data-ttu-id="bd516-103">Entity Framework 使用一组约定基于实体类的定义来构建模型。</span><span class="sxs-lookup"><span data-stu-id="bd516-103">Entity Framework uses a set of conventions to build a model based on the shape of your entity classes.</span></span> <span data-ttu-id="bd516-104">可指定其他配置以补充和/或替代约定的内容。</span><span class="sxs-lookup"><span data-stu-id="bd516-104">You can specify additional configuration to supplement and/or override what was discovered by convention.</span></span>

<span data-ttu-id="bd516-105">本文介绍可应用于面向任何数据存储的模型的配置，以及面向任意关系数据库时可应用的配置。</span><span class="sxs-lookup"><span data-stu-id="bd516-105">This article covers configuration that can be applied to a model targeting any data store and that which can be applied when targeting any relational database.</span></span> <span data-ttu-id="bd516-106">提供程序还可支持特定于具体数据存储的配置。</span><span class="sxs-lookup"><span data-stu-id="bd516-106">Providers may also enable configuration that is specific to a particular data store.</span></span> <span data-ttu-id="bd516-107">有关提供程序特定配置的文档，请参阅 [数据库提供程序](../providers/index.md) 部分。</span><span class="sxs-lookup"><span data-stu-id="bd516-107">For documentation on provider specific configuration see the [Database Providers](../providers/index.md) section.</span></span>

> [!TIP]  
> <span data-ttu-id="bd516-108">可在 GitHub 上查看此文章的 [示例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples) 。</span><span class="sxs-lookup"><span data-stu-id="bd516-108">You can view this article’s [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples) on GitHub.</span></span>

## <a name="use-fluent-api-to-configure-a-model"></a><span data-ttu-id="bd516-109">使用 fluent API 配置模型</span><span class="sxs-lookup"><span data-stu-id="bd516-109">Use fluent API to configure a model</span></span>

<span data-ttu-id="bd516-110">可在派生上下文中覆写 `OnModelCreating` 方法，并使用 `ModelBuilder API` 来配置模型。</span><span class="sxs-lookup"><span data-stu-id="bd516-110">You can override the `OnModelCreating` method in your derived context and use the `ModelBuilder API` to configure your model.</span></span> <span data-ttu-id="bd516-111">此配置方法最为有效，并可在不修改实体类的情况下指定配置。</span><span class="sxs-lookup"><span data-stu-id="bd516-111">This is the most powerful method of configuration and allows configuration to be specified without modifying your entity classes.</span></span> <span data-ttu-id="bd516-112">Fluent API 配置具有最高优先级，并将替代约定和数据注释。</span><span class="sxs-lookup"><span data-stu-id="bd516-112">Fluent API configuration has the highest precedence and will override conventions and data annotations.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?highlight=11-13)]

## <a name="use-data-annotations-to-configure-a-model"></a><span data-ttu-id="bd516-113">使用数据注释来配置模型</span><span class="sxs-lookup"><span data-stu-id="bd516-113">Use data annotations to configure a model</span></span>

<span data-ttu-id="bd516-114">也可将特性（称为数据注释）应用于类和属性。</span><span class="sxs-lookup"><span data-stu-id="bd516-114">You can also apply attributes (known as Data Annotations) to your classes and properties.</span></span> <span data-ttu-id="bd516-115">数据注释会替代约定，但会被 Fluent API 配置替代。</span><span class="sxs-lookup"><span data-stu-id="bd516-115">Data annotations will override conventions, but will be overridden by Fluent API configuration.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?highlight=14)]
