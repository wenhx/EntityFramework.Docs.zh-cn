---
title: 查询数据 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 7c65ec3e-46c8-48f8-8232-9e31f96c277b
uid: core/querying/index
ms.openlocfilehash: 51aaa5de11d3fe38b4fba82db8dcb5658088cc27
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993529"
---
# <a name="querying-data"></a><span data-ttu-id="30271-102">查询数据</span><span class="sxs-lookup"><span data-stu-id="30271-102">Querying Data</span></span>

<span data-ttu-id="30271-103">Entity Framework Core 使用语言集成查询 (LINQ) 来查询数据库中的数据。</span><span class="sxs-lookup"><span data-stu-id="30271-103">Entity Framework Core uses Language Integrated Query (LINQ) to query data from the database.</span></span> <span data-ttu-id="30271-104">通过 LINQ 可使用 C#（或你选择的其他 .NET 语言）基于派生上下文和实体类编写强类型查询。</span><span class="sxs-lookup"><span data-stu-id="30271-104">LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries based on your derived context and entity classes.</span></span> <span data-ttu-id="30271-105">LINQ 查询的一种表示形式会传递给数据库提供程序，进而转换为特定于数据库的查询语言（例如，适用于关系数据库的 SQL）。</span><span class="sxs-lookup"><span data-stu-id="30271-105">A representation of the LINQ query is passed to the database provider, to be translated in database-specific query language (for example, SQL for a relational database).</span></span> <span data-ttu-id="30271-106">有关如何处理查询的更多详细信息，请参阅[查询的工作原理](overview.md)。</span><span class="sxs-lookup"><span data-stu-id="30271-106">For more detailed information on how a query is processed, see [How Query Works](overview.md).</span></span>
