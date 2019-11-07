---
title: Foreign Key 约束-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: dbaf4bac-1fd5-46c0-ac57-64d7153bc574
uid: core/modeling/relational/fk-constraints
ms.openlocfilehash: df739f01a799ec8edad4cf44d8cf50edf292992f
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656001"
---
# <a name="foreign-key-constraints"></a><span data-ttu-id="1b7b6-102">外键约束</span><span class="sxs-lookup"><span data-stu-id="1b7b6-102">Foreign Key Constraints</span></span>

> [!NOTE]  
> <span data-ttu-id="1b7b6-103">一般而言，本部分中的配置适用于关系数据库。</span><span class="sxs-lookup"><span data-stu-id="1b7b6-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="1b7b6-104">安装关系数据库提供程序时，此处显示的扩展方法将变为可用（原因在于共享的 Microsoft.EntityFrameworkCore.Relational 包）。</span><span class="sxs-lookup"><span data-stu-id="1b7b6-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="1b7b6-105">为模型中的每个关系引入了 foreign key 约束。</span><span class="sxs-lookup"><span data-stu-id="1b7b6-105">A foreign key constraint is introduced for each relationship in the model.</span></span>

## <a name="conventions"></a><span data-ttu-id="1b7b6-106">约定</span><span class="sxs-lookup"><span data-stu-id="1b7b6-106">Conventions</span></span>

<span data-ttu-id="1b7b6-107">按照约定，外键约束命名 `FK_<dependent type name>_<principal type name>_<foreign key property name>`。</span><span class="sxs-lookup"><span data-stu-id="1b7b6-107">By convention, foreign key constraints are named `FK_<dependent type name>_<principal type name>_<foreign key property name>`.</span></span> <span data-ttu-id="1b7b6-108">对于复合外键 `<foreign key property name>` 成为外键属性名称的以下划线分隔的列表。</span><span class="sxs-lookup"><span data-stu-id="1b7b6-108">For composite foreign keys `<foreign key property name>` becomes an underscore separated list of foreign key property names.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="1b7b6-109">数据注释</span><span class="sxs-lookup"><span data-stu-id="1b7b6-109">Data Annotations</span></span>

<span data-ttu-id="1b7b6-110">不能使用数据批注配置 Foreign key 约束名称。</span><span class="sxs-lookup"><span data-stu-id="1b7b6-110">Foreign key constraint names cannot be configured using data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="1b7b6-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="1b7b6-111">Fluent API</span></span>

<span data-ttu-id="1b7b6-112">您可以使用熟知的 API 来配置关系的外键约束名称。</span><span class="sxs-lookup"><span data-stu-id="1b7b6-112">You can use the Fluent API to configure the foreign key constraint name for a relationship.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/RelationshipConstraintName.cs?name=Constraint&highlight=12)]
