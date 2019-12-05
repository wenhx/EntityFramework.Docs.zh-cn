---
title: Foreign Key 约束-EF Core
author: AndriySvyryd
ms.date: 11/21/2019
uid: core/modeling/relational/fk-constraints
ms.openlocfilehash: 2855137adf2ba3c9edaabd15a05f7a209f00f685
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824591"
---
# <a name="foreign-key-constraints"></a><span data-ttu-id="3a9f7-102">外键约束</span><span class="sxs-lookup"><span data-stu-id="3a9f7-102">Foreign Key Constraints</span></span>

> [!NOTE]  
> <span data-ttu-id="3a9f7-103">一般而言，本部分中的配置适用于关系数据库。</span><span class="sxs-lookup"><span data-stu-id="3a9f7-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="3a9f7-104">安装关系数据库提供程序时，此处显示的扩展方法将变为可用（原因在于共享的 Microsoft.EntityFrameworkCore.Relational 包）。</span><span class="sxs-lookup"><span data-stu-id="3a9f7-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="3a9f7-105">为模型中的每个关系引入了 foreign key 约束。</span><span class="sxs-lookup"><span data-stu-id="3a9f7-105">A foreign key constraint is introduced for each relationship in the model.</span></span>

## <a name="conventions"></a><span data-ttu-id="3a9f7-106">约定</span><span class="sxs-lookup"><span data-stu-id="3a9f7-106">Conventions</span></span>

<span data-ttu-id="3a9f7-107">按照约定，外键约束命名 `FK_<dependent type name>_<principal type name>_<foreign key property name>`。</span><span class="sxs-lookup"><span data-stu-id="3a9f7-107">By convention, foreign key constraints are named `FK_<dependent type name>_<principal type name>_<foreign key property name>`.</span></span> <span data-ttu-id="3a9f7-108">对于复合外键 `<foreign key property name>` 成为外键属性名称的以下划线分隔的列表。</span><span class="sxs-lookup"><span data-stu-id="3a9f7-108">For composite foreign keys `<foreign key property name>` becomes an underscore separated list of foreign key property names.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="3a9f7-109">数据注释</span><span class="sxs-lookup"><span data-stu-id="3a9f7-109">Data Annotations</span></span>

<span data-ttu-id="3a9f7-110">不能使用数据批注配置 Foreign key 约束名称。</span><span class="sxs-lookup"><span data-stu-id="3a9f7-110">Foreign key constraint names cannot be configured using data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="3a9f7-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="3a9f7-111">Fluent API</span></span>

<span data-ttu-id="3a9f7-112">您可以使用熟知的 API 来配置关系的外键约束名称。</span><span class="sxs-lookup"><span data-stu-id="3a9f7-112">You can use the Fluent API to configure the foreign key constraint name for a relationship.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/RelationshipConstraintName.cs?name=Constraint&highlight=12)]
