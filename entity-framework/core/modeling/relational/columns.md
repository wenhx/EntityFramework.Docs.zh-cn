---
title: 列映射的 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 05a47de9-1078-488e-a823-b516a4208f33
uid: core/modeling/relational/columns
ms.openlocfilehash: 22fcafbfcf9daf765c94e6ca9c42d7770d3e7d07
ms.sourcegitcommit: 87fcaba46535aa351db4bdb1231bd14b40e459b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2019
ms.locfileid: "59929857"
---
# <a name="column-mapping"></a>列映射

> [!NOTE]  
> 一般而言，本部分中的配置适用于关系数据库。 安装关系数据库提供程序时，此处显示的扩展方法将变为可用（原因在于共享的 Microsoft.EntityFrameworkCore.Relational 包）。

列映射标识应从查询的列数据，并将其保存到数据库中。

## <a name="conventions"></a>约定

按照约定，每个属性将会设置映射到具有相同名称的属性列。

## <a name="data-annotations"></a>数据注释

可以使用数据注释来配置属性映射到其中的列。

[!code-csharp[Main](../../../../samples/core/Modeling/DataAnnotations/Samples/Relational/Column.cs?highlight=13)]

## <a name="fluent-api"></a>Fluent API

可以使用 Fluent API 配置属性映射到其中的列。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/Column.cs?highlight=11-13)]
