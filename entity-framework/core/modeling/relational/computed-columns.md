---
title: 计算列-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e9d81f06-805d-45c9-97c2-3546df654829
uid: core/modeling/relational/computed-columns
ms.openlocfilehash: 4e92ed6d785f3b7bdf54015101bdcddb9e4e0e1c
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655921"
---
# <a name="computed-columns"></a><span data-ttu-id="e16c2-102">计算列</span><span class="sxs-lookup"><span data-stu-id="e16c2-102">Computed Columns</span></span>

> [!NOTE]  
> <span data-ttu-id="e16c2-103">一般而言，本部分中的配置适用于关系数据库。</span><span class="sxs-lookup"><span data-stu-id="e16c2-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="e16c2-104">安装关系数据库提供程序时，此处显示的扩展方法将变为可用（原因在于共享的 Microsoft.EntityFrameworkCore.Relational 包）。</span><span class="sxs-lookup"><span data-stu-id="e16c2-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="e16c2-105">计算列是在数据库中计算其值的列。</span><span class="sxs-lookup"><span data-stu-id="e16c2-105">A computed column is a column whose value is calculated in the database.</span></span> <span data-ttu-id="e16c2-106">计算列可以使用表中的其他列来计算其值。</span><span class="sxs-lookup"><span data-stu-id="e16c2-106">A computed column can use other columns in the table to calculate its value.</span></span>

## <a name="conventions"></a><span data-ttu-id="e16c2-107">约定</span><span class="sxs-lookup"><span data-stu-id="e16c2-107">Conventions</span></span>

<span data-ttu-id="e16c2-108">按照约定，不会在模型中创建计算列。</span><span class="sxs-lookup"><span data-stu-id="e16c2-108">By convention, computed columns are not created in the model.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="e16c2-109">数据注释</span><span class="sxs-lookup"><span data-stu-id="e16c2-109">Data Annotations</span></span>

<span data-ttu-id="e16c2-110">计算列不能配置数据批注。</span><span class="sxs-lookup"><span data-stu-id="e16c2-110">Computed columns can not be configured with Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="e16c2-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="e16c2-111">Fluent API</span></span>

<span data-ttu-id="e16c2-112">您可以使用熟知的 API 来指定属性应映射到计算列。</span><span class="sxs-lookup"><span data-stu-id="e16c2-112">You can use the Fluent API to specify that a property should map to a computed column.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/ComputedColumn.cs?name=ComputedColumn&highlight=9)]
