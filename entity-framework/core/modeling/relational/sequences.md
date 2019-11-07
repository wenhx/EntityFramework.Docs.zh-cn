---
title: 序列-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 94f81a92-3c72-4e14-912a-f99310374e42
uid: core/modeling/relational/sequences
ms.openlocfilehash: b810caaffa329bb5ad6f3486145d0ade9287eada
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656117"
---
# <a name="sequences"></a><span data-ttu-id="fd13f-102">序列</span><span class="sxs-lookup"><span data-stu-id="fd13f-102">Sequences</span></span>

> [!NOTE]  
> <span data-ttu-id="fd13f-103">一般而言，本部分中的配置适用于关系数据库。</span><span class="sxs-lookup"><span data-stu-id="fd13f-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="fd13f-104">安装关系数据库提供程序时，此处显示的扩展方法将变为可用（原因在于共享的 Microsoft.EntityFrameworkCore.Relational 包）。</span><span class="sxs-lookup"><span data-stu-id="fd13f-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="fd13f-105">序列将在数据库中生成一个顺序数值。</span><span class="sxs-lookup"><span data-stu-id="fd13f-105">A sequence generates a sequential numeric values in the database.</span></span> <span data-ttu-id="fd13f-106">序列不与特定表相关联。</span><span class="sxs-lookup"><span data-stu-id="fd13f-106">Sequences are not associated with a specific table.</span></span>

## <a name="conventions"></a><span data-ttu-id="fd13f-107">约定</span><span class="sxs-lookup"><span data-stu-id="fd13f-107">Conventions</span></span>

<span data-ttu-id="fd13f-108">按照约定，序列不会引入到模型中。</span><span class="sxs-lookup"><span data-stu-id="fd13f-108">By convention, sequences are not introduced in to the model.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="fd13f-109">数据注释</span><span class="sxs-lookup"><span data-stu-id="fd13f-109">Data Annotations</span></span>

<span data-ttu-id="fd13f-110">不能使用数据批注来配置序列。</span><span class="sxs-lookup"><span data-stu-id="fd13f-110">You can not configure a sequence using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="fd13f-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="fd13f-111">Fluent API</span></span>

<span data-ttu-id="fd13f-112">您可以使用熟知的 API 在模型中创建一个序列。</span><span class="sxs-lookup"><span data-stu-id="fd13f-112">You can use the Fluent API to create a sequence in the model.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/Sequence.cs?name=Model&highlight=7)]

<span data-ttu-id="fd13f-113">你还可以配置序列的其他方面，例如其架构、起始值和增量。</span><span class="sxs-lookup"><span data-stu-id="fd13f-113">You can also configure additional aspect of the sequence, such as its schema, start value, and increment.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/SequenceConfigured.cs?name=Sequence&highlight=7,8,9)]

<span data-ttu-id="fd13f-114">引入序列后，可以使用它为模型中的属性生成值。</span><span class="sxs-lookup"><span data-stu-id="fd13f-114">Once a sequence is introduced, you can use it to generate values for properties in your model.</span></span> <span data-ttu-id="fd13f-115">例如，您可以使用[默认值](default-values.md)从序列中插入下一个值。</span><span class="sxs-lookup"><span data-stu-id="fd13f-115">For example, you can use [Default Values](default-values.md) to insert the next value from the sequence.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/SequenceUsed.cs?name=Default&highlight=13)]
