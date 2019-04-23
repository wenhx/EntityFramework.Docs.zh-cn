---
title: 列映射的 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 05a47de9-1078-488e-a823-b516a4208f33
uid: core/modeling/relational/columns
ms.openlocfilehash: 22fcafbfcf9daf765c94e6ca9c42d7770d3e7d07
ms.sourcegitcommit: 87fcaba46535aa351db4bdb1231bd14b40e459b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2019
ms.locfileid: "59929857"
---
# <a name="column-mapping"></a><span data-ttu-id="68b8e-102">列映射</span><span class="sxs-lookup"><span data-stu-id="68b8e-102">Column Mapping</span></span>

> [!NOTE]  
> <span data-ttu-id="68b8e-103">一般而言，本部分中的配置适用于关系数据库。</span><span class="sxs-lookup"><span data-stu-id="68b8e-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="68b8e-104">安装关系数据库提供程序时，此处显示的扩展方法将变为可用（原因在于共享的 Microsoft.EntityFrameworkCore.Relational 包）。</span><span class="sxs-lookup"><span data-stu-id="68b8e-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="68b8e-105">列映射标识应从查询的列数据，并将其保存到数据库中。</span><span class="sxs-lookup"><span data-stu-id="68b8e-105">Column mapping identifies which column data should be queried from and saved to in the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="68b8e-106">约定</span><span class="sxs-lookup"><span data-stu-id="68b8e-106">Conventions</span></span>

<span data-ttu-id="68b8e-107">按照约定，每个属性将会设置映射到具有相同名称的属性列。</span><span class="sxs-lookup"><span data-stu-id="68b8e-107">By convention, each property will be set up to map to a column with the same name as the property.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="68b8e-108">数据注释</span><span class="sxs-lookup"><span data-stu-id="68b8e-108">Data Annotations</span></span>

<span data-ttu-id="68b8e-109">可以使用数据注释来配置属性映射到其中的列。</span><span class="sxs-lookup"><span data-stu-id="68b8e-109">You can use Data Annotations to configure the column to which a property is mapped.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/DataAnnotations/Samples/Relational/Column.cs?highlight=13)]

## <a name="fluent-api"></a><span data-ttu-id="68b8e-110">Fluent API</span><span class="sxs-lookup"><span data-stu-id="68b8e-110">Fluent API</span></span>

<span data-ttu-id="68b8e-111">可以使用 Fluent API 配置属性映射到其中的列。</span><span class="sxs-lookup"><span data-stu-id="68b8e-111">You can use the Fluent API to configure the column to which a property is mapped.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/Column.cs?highlight=11-13)]
