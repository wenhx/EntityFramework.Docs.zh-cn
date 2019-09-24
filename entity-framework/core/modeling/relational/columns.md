---
title: 列映射-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 05a47de9-1078-488e-a823-b516a4208f33
uid: core/modeling/relational/columns
ms.openlocfilehash: eaffc0cc1642f64edabeeef974f5f6de7a23b656
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197211"
---
# <a name="column-mapping"></a><span data-ttu-id="fe04a-102">列映射</span><span class="sxs-lookup"><span data-stu-id="fe04a-102">Column Mapping</span></span>

> [!NOTE]  
> <span data-ttu-id="fe04a-103">一般而言，本部分中的配置适用于关系数据库。</span><span class="sxs-lookup"><span data-stu-id="fe04a-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="fe04a-104">安装关系数据库提供程序时，此处显示的扩展方法将变为可用（原因在于共享的 Microsoft.EntityFrameworkCore.Relational 包）。</span><span class="sxs-lookup"><span data-stu-id="fe04a-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="fe04a-105">列映射标识在数据库中应从哪些列数据中进行查询和保存。</span><span class="sxs-lookup"><span data-stu-id="fe04a-105">Column mapping identifies which column data should be queried from and saved to in the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="fe04a-106">约定</span><span class="sxs-lookup"><span data-stu-id="fe04a-106">Conventions</span></span>

<span data-ttu-id="fe04a-107">按照约定，每个属性将会设置为映射到与属性具有相同名称的列。</span><span class="sxs-lookup"><span data-stu-id="fe04a-107">By convention, each property will be set up to map to a column with the same name as the property.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="fe04a-108">数据注释</span><span class="sxs-lookup"><span data-stu-id="fe04a-108">Data Annotations</span></span>

<span data-ttu-id="fe04a-109">可以使用数据注释来配置属性映射到的那一列。</span><span class="sxs-lookup"><span data-stu-id="fe04a-109">You can use Data Annotations to configure the column to which a property is mapped.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/DataAnnotations/Relational/Column.cs?highlight=13)]

## <a name="fluent-api"></a><span data-ttu-id="fe04a-110">Fluent API</span><span class="sxs-lookup"><span data-stu-id="fe04a-110">Fluent API</span></span>

<span data-ttu-id="fe04a-111">您可以使用熟知的 API 来配置属性映射到的列。</span><span class="sxs-lookup"><span data-stu-id="fe04a-111">You can use the Fluent API to configure the column to which a property is mapped.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/Column.cs?highlight=11-13)]
