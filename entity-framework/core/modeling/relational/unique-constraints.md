---
title: 备用键（唯一约束）-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 3d419dcf-2b5d-467c-b408-ea03d830721a
uid: core/modeling/relational/unique-constraints
ms.openlocfilehash: 7afcb804aeeccbd5e07c228a8fd9850ca00a2919
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197613"
---
# <a name="alternate-keys-unique-constraints"></a><span data-ttu-id="0a760-102">备用键（唯一约束）</span><span class="sxs-lookup"><span data-stu-id="0a760-102">Alternate Keys (Unique Constraints)</span></span>

> [!NOTE]  
> <span data-ttu-id="0a760-103">一般而言，本部分中的配置适用于关系数据库。</span><span class="sxs-lookup"><span data-stu-id="0a760-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="0a760-104">安装关系数据库提供程序时，此处显示的扩展方法将变为可用（原因在于共享的 Microsoft.EntityFrameworkCore.Relational 包）。</span><span class="sxs-lookup"><span data-stu-id="0a760-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="0a760-105">为模型中的每个备用键引入唯一约束。</span><span class="sxs-lookup"><span data-stu-id="0a760-105">A unique constraint is introduced for each alternate key in the model.</span></span>

## <a name="conventions"></a><span data-ttu-id="0a760-106">约定</span><span class="sxs-lookup"><span data-stu-id="0a760-106">Conventions</span></span>

<span data-ttu-id="0a760-107">按照约定，为备用键引入的索引和约束将命名为`AK_<type name>_<property name>`。</span><span class="sxs-lookup"><span data-stu-id="0a760-107">By convention, the index and constraint that are introduced for an alternate key will be named `AK_<type name>_<property name>`.</span></span> <span data-ttu-id="0a760-108">对于复合备用键`<property name>` ，将变成以下划线分隔的属性名称列表。</span><span class="sxs-lookup"><span data-stu-id="0a760-108">For composite alternate keys `<property name>` becomes an underscore separated list of property names.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="0a760-109">数据注释</span><span class="sxs-lookup"><span data-stu-id="0a760-109">Data Annotations</span></span>

<span data-ttu-id="0a760-110">不能使用数据批注配置 Unique 约束。</span><span class="sxs-lookup"><span data-stu-id="0a760-110">Unique constraints can not be configured using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="0a760-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="0a760-111">Fluent API</span></span>

<span data-ttu-id="0a760-112">您可以使用熟知的 API 来配置备用密钥的索引和约束名称。</span><span class="sxs-lookup"><span data-stu-id="0a760-112">You can use the Fluent API to configure the index and constraint name for an alternate key.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/AlternateKeyName.cs?name=Model&highlight=9)]
