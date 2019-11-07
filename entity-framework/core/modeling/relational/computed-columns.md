---
title: 计算列-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e9d81f06-805d-45c9-97c2-3546df654829
uid: core/modeling/relational/computed-columns
ms.openlocfilehash: 4e92ed6d785f3b7bdf54015101bdcddb9e4e0e1c
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655921"
---
# <a name="computed-columns"></a>计算列

> [!NOTE]  
> 一般而言，本部分中的配置适用于关系数据库。 安装关系数据库提供程序时，此处显示的扩展方法将变为可用（原因在于共享的 Microsoft.EntityFrameworkCore.Relational 包）。

计算列是在数据库中计算其值的列。 计算列可以使用表中的其他列来计算其值。

## <a name="conventions"></a>约定

按照约定，不会在模型中创建计算列。

## <a name="data-annotations"></a>数据注释

计算列不能配置数据批注。

## <a name="fluent-api"></a>Fluent API

您可以使用熟知的 API 来指定属性应映射到计算列。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/ComputedColumn.cs?name=ComputedColumn&highlight=9)]
