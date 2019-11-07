---
title: 默认架构-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e6e58473-9f5e-4a1f-ac0f-b87d2cbb667e
uid: core/modeling/relational/default-schema
ms.openlocfilehash: 1579fed007997aa4cf49b4c1290aee86c81c0000
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655972"
---
# <a name="default-schema"></a><span data-ttu-id="fb80a-102">默认架构</span><span class="sxs-lookup"><span data-stu-id="fb80a-102">Default Schema</span></span>

> [!NOTE]  
> <span data-ttu-id="fb80a-103">一般而言，本部分中的配置适用于关系数据库。</span><span class="sxs-lookup"><span data-stu-id="fb80a-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="fb80a-104">安装关系数据库提供程序时，此处显示的扩展方法将变为可用（原因在于共享的 Microsoft.EntityFrameworkCore.Relational 包）。</span><span class="sxs-lookup"><span data-stu-id="fb80a-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="fb80a-105">如果没有为该对象显式配置架构，则默认架构为将在其中创建对象的数据库架构。</span><span class="sxs-lookup"><span data-stu-id="fb80a-105">The default schema is the database schema that objects will be created in if a schema is not explicitly configured for that object.</span></span>

## <a name="conventions"></a><span data-ttu-id="fb80a-106">约定</span><span class="sxs-lookup"><span data-stu-id="fb80a-106">Conventions</span></span>

<span data-ttu-id="fb80a-107">按照约定，数据库提供程序将选择最适合的默认架构。</span><span class="sxs-lookup"><span data-stu-id="fb80a-107">By convention, the database provider will choose the most appropriate default schema.</span></span> <span data-ttu-id="fb80a-108">例如，Microsoft SQL Server 将使用 `dbo` 架构，SQLite 将不使用架构（因为 SQLite 不支持架构）。</span><span class="sxs-lookup"><span data-stu-id="fb80a-108">For example, Microsoft SQL Server will use the `dbo` schema and SQLite will not use a schema (since schemas are not supported in SQLite).</span></span>

## <a name="data-annotations"></a><span data-ttu-id="fb80a-109">数据注释</span><span class="sxs-lookup"><span data-stu-id="fb80a-109">Data Annotations</span></span>

<span data-ttu-id="fb80a-110">不能使用数据批注设置默认架构。</span><span class="sxs-lookup"><span data-stu-id="fb80a-110">You can not set the default schema using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="fb80a-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="fb80a-111">Fluent API</span></span>

<span data-ttu-id="fb80a-112">可以使用 "熟知 API" 来指定默认架构。</span><span class="sxs-lookup"><span data-stu-id="fb80a-112">You can use the Fluent API to specify a default schema.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/DefaultSchema.cs?name=DefaultSchema&highlight=7)]
