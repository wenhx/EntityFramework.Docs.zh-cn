---
title: 数据类型-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 9d2e647f-29e4-483b-af00-74269eb06e8f
uid: core/modeling/relational/data-types
ms.openlocfilehash: 26664ebe18abcdeaa2b9c8dc23a6410204f53c8e
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197177"
---
# <a name="data-types"></a>数据类型

> [!NOTE]  
> 一般而言，本部分中的配置适用于关系数据库。 安装关系数据库提供程序时，此处显示的扩展方法将变为可用（原因在于共享的 Microsoft.EntityFrameworkCore.Relational 包）。

数据类型是指属性所映射到的列的数据库特定类型。

## <a name="conventions"></a>约定

按照约定，数据库提供程序基于属性的 .NET 类型选择数据类型。 它还会考虑其他元数据，如配置的[最大长度](../max-length.md)、属性是否是主键的一部分等。

`datetime2(7)`例如，SQL Server `DateTime`用于属性和`nvarchar(max)` `string`属性（ `nvarchar(450)` 或`string`用作键的属性）。

## <a name="data-annotations"></a>数据注释

您可以使用数据批注为列指定精确的数据类型。

例如，下面的代码将`Url`配置为一个非 unicode 字符串，其最大`200`长度`Rating`为，小数位数`2`为`5` ，小数位数为。

[!code-csharp[Main](../../../../samples/core/Modeling/DataAnnotations/Relational/DataType.cs?name=Entities&highlight=4,6)]

## <a name="fluent-api"></a>Fluent API

你还可以使用熟知的 API 为列指定相同的数据类型。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/DataType.cs?name=Model&highlight=9-10)]
