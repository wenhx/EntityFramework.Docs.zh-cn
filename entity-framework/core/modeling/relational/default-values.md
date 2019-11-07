---
title: 默认值-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e541366a-130f-47dd-9997-1b110a11febe
uid: core/modeling/relational/default-values
ms.openlocfilehash: b6ac283d551e2c6ee119f7de6933363b5d8793a1
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655900"
---
# <a name="default-values"></a><span data-ttu-id="6189b-102">默认值</span><span class="sxs-lookup"><span data-stu-id="6189b-102">Default Values</span></span>

> [!NOTE]  
> <span data-ttu-id="6189b-103">一般而言，本部分中的配置适用于关系数据库。</span><span class="sxs-lookup"><span data-stu-id="6189b-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="6189b-104">安装关系数据库提供程序时，此处显示的扩展方法将变为可用（原因在于共享的 Microsoft.EntityFrameworkCore.Relational 包）。</span><span class="sxs-lookup"><span data-stu-id="6189b-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="6189b-105">如果插入新行，但没有为该列指定值，则列的默认值为要插入的值。</span><span class="sxs-lookup"><span data-stu-id="6189b-105">The default value of a column is the value that will be inserted if a new row is inserted but no value is specified for the column.</span></span>

## <a name="conventions"></a><span data-ttu-id="6189b-106">约定</span><span class="sxs-lookup"><span data-stu-id="6189b-106">Conventions</span></span>

<span data-ttu-id="6189b-107">按照约定，未配置默认值。</span><span class="sxs-lookup"><span data-stu-id="6189b-107">By convention, a default value is not configured.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="6189b-108">数据注释</span><span class="sxs-lookup"><span data-stu-id="6189b-108">Data Annotations</span></span>

<span data-ttu-id="6189b-109">不能使用数据批注设置默认值。</span><span class="sxs-lookup"><span data-stu-id="6189b-109">You can not set a default value using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="6189b-110">Fluent API</span><span class="sxs-lookup"><span data-stu-id="6189b-110">Fluent API</span></span>

<span data-ttu-id="6189b-111">您可以使用 "熟知 API" 指定属性的默认值。</span><span class="sxs-lookup"><span data-stu-id="6189b-111">You can use the Fluent API to specify the default value for a property.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/DefaultValue.cs?name=DefaultValue&highlight=9)]

<span data-ttu-id="6189b-112">还可以指定用于计算默认值的 SQL 片段。</span><span class="sxs-lookup"><span data-stu-id="6189b-112">You can also specify a SQL fragment that is used to calculate the default value.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/DefaultValueSql.cs?name=DefaultValueSql&highlight=9)]
