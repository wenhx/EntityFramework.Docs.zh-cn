---
title: 最大长度-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c39c5d43-018d-48b8-94f2-b8bc7c686c69
uid: core/modeling/max-length
ms.openlocfilehash: b6f0594fed0c491b4f79dcda5273cdebe9ecf35f
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197221"
---
# <a name="maximum-length"></a>最大长度

配置最大长度可为数据存储提供有关要对给定属性使用的相应数据类型的提示。 最大长度仅适用于数组数据类型，如 `string` 和 `byte[]`。

> [!NOTE]  
> 将数据传递到提供程序之前，实体框架不会执行任何最大长度验证。 由提供程序或数据存储在适当时机进行验证。 例如，如果以 SQL Server 为目标，超过最大长度将引发异常，因为基础列的数据类型不允许存储过多数据。

## <a name="conventions"></a>约定

按照约定，应由数据库提供程序为属性选择适当的数据类型。 对于具有长度的属性，数据库提供程序通常将选择允许最长数据长度的数据类型。 例如，Microsoft SQL Server 将对字符`string`属性使用 `nvarchar(max)`（如果该列用作键，则会使用 `nvarchar(450)`）。

## <a name="data-annotations"></a>数据注释

您可以使用数据批注为属性配置最大长度。 此示例面向 SQL Server，因此使用数据类型 `nvarchar(500)`。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/MaxLength.cs?highlight=14)]

## <a name="fluent-api"></a>Fluent API

您可以使用熟知的 API 来配置属性的最大长度。 此示例面向 SQL Server，因此使用数据类型 `nvarchar(500)`。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/MaxLength.cs?highlight=11-13)]
