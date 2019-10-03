---
title: 索引（关系数据库）-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 4581e7ba-5e7f-452c-9937-0aaf790ba10a
uid: core/modeling/relational/indexes
ms.openlocfilehash: 7bb74d0bfa6090b597eb988a46f00494e25f233e
ms.sourcegitcommit: 6c28926a1e35e392b198a8729fc13c1c1968a27b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71813635"
---
# <a name="indexes-relational-database"></a><span data-ttu-id="c0304-102">索引（关系数据库）</span><span class="sxs-lookup"><span data-stu-id="c0304-102">Indexes (Relational Database)</span></span>

> [!NOTE]  
> <span data-ttu-id="c0304-103">一般而言，本部分中的配置适用于关系数据库。</span><span class="sxs-lookup"><span data-stu-id="c0304-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="c0304-104">安装关系数据库提供程序后，此处显示的扩展方法将变为可用（原因在于 Microsoft.EntityFrameworkCore.Relational 包是共享的）。</span><span class="sxs-lookup"><span data-stu-id="c0304-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="c0304-105">关系数据库中的索引映射到与实体框架核心中的索引相同的概念。</span><span class="sxs-lookup"><span data-stu-id="c0304-105">An index in a relational database maps to the same concept as an index in the core of Entity Framework.</span></span>

## <a name="conventions"></a><span data-ttu-id="c0304-106">约定</span><span class="sxs-lookup"><span data-stu-id="c0304-106">Conventions</span></span>

<span data-ttu-id="c0304-107">按照约定，索引命名为`IX_<type name>_<property name>`。</span><span class="sxs-lookup"><span data-stu-id="c0304-107">By convention, indexes are named `IX_<type name>_<property name>`.</span></span> <span data-ttu-id="c0304-108">对于复合索引`<property name>` ，将成为以下划线分隔的属性名称列表。</span><span class="sxs-lookup"><span data-stu-id="c0304-108">For composite indexes `<property name>` becomes an underscore separated list of property names.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="c0304-109">数据注释</span><span class="sxs-lookup"><span data-stu-id="c0304-109">Data Annotations</span></span>

<span data-ttu-id="c0304-110">不能使用数据批注配置索引。</span><span class="sxs-lookup"><span data-stu-id="c0304-110">Indexes can not be configured using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="c0304-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="c0304-111">Fluent API</span></span>

<span data-ttu-id="c0304-112">您可以使用熟知的 API 来配置索引的名称。</span><span class="sxs-lookup"><span data-stu-id="c0304-112">You can use the Fluent API to configure the name of an index.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/IndexName.cs?name=Model&highlight=9)]

<span data-ttu-id="c0304-113">您还可以指定筛选器。</span><span class="sxs-lookup"><span data-stu-id="c0304-113">You can also specify a filter.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/IndexFilter.cs?name=Model&highlight=9)]

<span data-ttu-id="c0304-114">使用 SQL Server 提供程序 EF 为唯一索引中包含的所有可以为 null 的列添加 "IS NOT NULL" 筛选器。</span><span class="sxs-lookup"><span data-stu-id="c0304-114">When using the SQL Server provider EF adds a 'IS NOT NULL' filter for all nullable columns that are part of a unique index.</span></span> <span data-ttu-id="c0304-115">若要重写此约定，可以`null`提供一个值。</span><span class="sxs-lookup"><span data-stu-id="c0304-115">To override this convention you can supply a `null` value.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/IndexNoFilter.cs?name=Model&highlight=10)]

### <a name="include-columns-in-sql-server-indexes"></a><span data-ttu-id="c0304-116">在 SQL Server 索引中包含列</span><span class="sxs-lookup"><span data-stu-id="c0304-116">Include Columns in SQL Server Indexes</span></span>

<span data-ttu-id="c0304-117">当查询中的所有列都作为键列或非键列包含在索引中时，可以[通过包含列配置索引](https://docs.microsoft.com/sql/relational-databases/indexes/create-indexes-with-included-columns)以显著提高查询性能。</span><span class="sxs-lookup"><span data-stu-id="c0304-117">You can configure [indexes with included columns](https://docs.microsoft.com/sql/relational-databases/indexes/create-indexes-with-included-columns) to significantly improve query performance when all columns in the query are included in the index as key or non-key columns.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/ForSqlServerHasIndex.cs?name=Model)]
