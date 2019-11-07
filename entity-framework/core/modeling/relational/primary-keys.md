---
title: 主键-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c78f8f42-564a-45a4-aca7-3ede9f7ed2bc
uid: core/modeling/relational/primary-keys
ms.openlocfilehash: c195cc37859ea0689b5c6fbb84051564cf96c83a
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656053"
---
# <a name="primary-keys"></a><span data-ttu-id="3c250-102">主键</span><span class="sxs-lookup"><span data-stu-id="3c250-102">Primary Keys</span></span>

> [!NOTE]  
> <span data-ttu-id="3c250-103">一般而言，本部分中的配置适用于关系数据库。</span><span class="sxs-lookup"><span data-stu-id="3c250-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="3c250-104">安装关系数据库提供程序时，此处显示的扩展方法将变为可用（原因在于共享的 Microsoft.EntityFrameworkCore.Relational 包）。</span><span class="sxs-lookup"><span data-stu-id="3c250-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="3c250-105">为每个实体类型的键引入 primary key 约束。</span><span class="sxs-lookup"><span data-stu-id="3c250-105">A primary key constraint is introduced for the key of each entity type.</span></span>

## <a name="conventions"></a><span data-ttu-id="3c250-106">约定</span><span class="sxs-lookup"><span data-stu-id="3c250-106">Conventions</span></span>

<span data-ttu-id="3c250-107">按照约定，会将数据库中的主键命名为 `PK_<type name>`。</span><span class="sxs-lookup"><span data-stu-id="3c250-107">By convention, the primary key in the database will be named `PK_<type name>`.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="3c250-108">数据注释</span><span class="sxs-lookup"><span data-stu-id="3c250-108">Data Annotations</span></span>

<span data-ttu-id="3c250-109">不能使用数据批注配置主键的关系数据库的特定方面。</span><span class="sxs-lookup"><span data-stu-id="3c250-109">No relational database specific aspects of a primary key can be configured using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="3c250-110">Fluent API</span><span class="sxs-lookup"><span data-stu-id="3c250-110">Fluent API</span></span>

<span data-ttu-id="3c250-111">您可以使用 "熟知 API" 在数据库中配置 primary key 约束的名称。</span><span class="sxs-lookup"><span data-stu-id="3c250-111">You can use the Fluent API to configure the name of the primary key constraint in the database.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/KeyName.cs?name=KeyName&highlight=9)]
