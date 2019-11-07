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
# <a name="sequences"></a>序列

> [!NOTE]  
> 一般而言，本部分中的配置适用于关系数据库。 安装关系数据库提供程序时，此处显示的扩展方法将变为可用（原因在于共享的 Microsoft.EntityFrameworkCore.Relational 包）。

序列将在数据库中生成一个顺序数值。 序列不与特定表相关联。

## <a name="conventions"></a>约定

按照约定，序列不会引入到模型中。

## <a name="data-annotations"></a>数据注释

不能使用数据批注来配置序列。

## <a name="fluent-api"></a>Fluent API

您可以使用熟知的 API 在模型中创建一个序列。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/Sequence.cs?name=Model&highlight=7)]

你还可以配置序列的其他方面，例如其架构、起始值和增量。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/SequenceConfigured.cs?name=Sequence&highlight=7,8,9)]

引入序列后，可以使用它为模型中的属性生成值。 例如，您可以使用[默认值](default-values.md)从序列中插入下一个值。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/SequenceUsed.cs?name=Default&highlight=13)]
