---
title: 数据类型-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 9d2e647f-29e4-483b-af00-74269eb06e8f
uid: core/modeling/relational/data-types
ms.openlocfilehash: d667cbcb821e321faed36d097b531c7c55b81248
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149160"
---
# <a name="data-types"></a><span data-ttu-id="fbb33-102">数据类型</span><span class="sxs-lookup"><span data-stu-id="fbb33-102">Data Types</span></span>

> [!NOTE]  
> <span data-ttu-id="fbb33-103">一般而言，本部分中的配置适用于关系数据库。</span><span class="sxs-lookup"><span data-stu-id="fbb33-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="fbb33-104">安装关系数据库提供程序时，此处显示的扩展方法将变为可用（原因在于共享的 Microsoft.EntityFrameworkCore.Relational 包）。</span><span class="sxs-lookup"><span data-stu-id="fbb33-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="fbb33-105">数据类型是指属性所映射到的列的数据库特定类型。</span><span class="sxs-lookup"><span data-stu-id="fbb33-105">Data type refers to the database specific type of the column to which a property is mapped.</span></span>

## <a name="conventions"></a><span data-ttu-id="fbb33-106">约定</span><span class="sxs-lookup"><span data-stu-id="fbb33-106">Conventions</span></span>

<span data-ttu-id="fbb33-107">按照约定，数据库提供程序基于属性的 .NET 类型选择数据类型。</span><span class="sxs-lookup"><span data-stu-id="fbb33-107">By convention, the database provider selects a data type based on the .NET type of the property.</span></span> <span data-ttu-id="fbb33-108">它还会考虑其他元数据，如配置的[最大长度](../max-length.md)、属性是否是主键的一部分等。</span><span class="sxs-lookup"><span data-stu-id="fbb33-108">It also takes into account other metadata, such as the configured [Maximum Length](../max-length.md), whether the property is part of a primary key, etc.</span></span>

<span data-ttu-id="fbb33-109">`datetime2(7)`例如，SQL Server `DateTime`用于属性和`nvarchar(max)` `string`属性（ `nvarchar(450)` 或`string`用作键的属性）。</span><span class="sxs-lookup"><span data-stu-id="fbb33-109">For example, SQL Server uses `datetime2(7)` for `DateTime` properties, and `nvarchar(max)` for `string` properties (or `nvarchar(450)` for `string` properties that are used as a key).</span></span>

## <a name="data-annotations"></a><span data-ttu-id="fbb33-110">数据注释</span><span class="sxs-lookup"><span data-stu-id="fbb33-110">Data Annotations</span></span>

<span data-ttu-id="fbb33-111">您可以使用数据批注为列指定精确的数据类型。</span><span class="sxs-lookup"><span data-stu-id="fbb33-111">You can use Data Annotations to specify an exact data type for a column.</span></span>

<span data-ttu-id="fbb33-112">例如，下面的代码将`Url`配置为一个非 unicode 字符串，其最大`200`长度`Rating`为，小数位数`2`为`5` ，小数位数为。</span><span class="sxs-lookup"><span data-stu-id="fbb33-112">For example the following code configures `Url` as a non-unicode string with maximum length of `200` and `Rating` as decimal with precision of `5` and scale of `2`.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/DataAnnotations/Samples/Relational/DataType.cs?name=Entities&highlight=4,6)]

## <a name="fluent-api"></a><span data-ttu-id="fbb33-113">Fluent API</span><span class="sxs-lookup"><span data-stu-id="fbb33-113">Fluent API</span></span>

<span data-ttu-id="fbb33-114">你还可以使用熟知的 API 为列指定相同的数据类型。</span><span class="sxs-lookup"><span data-stu-id="fbb33-114">You can also use the Fluent API to specify the same data types for the columns.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/DataType.cs?name=Model&highlight=9-10)]
