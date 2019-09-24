---
title: 备用键（唯一约束）-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 3d419dcf-2b5d-467c-b408-ea03d830721a
uid: core/modeling/relational/unique-constraints
ms.openlocfilehash: 7afcb804aeeccbd5e07c228a8fd9850ca00a2919
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197613"
---
# <a name="alternate-keys-unique-constraints"></a>备用键（唯一约束）

> [!NOTE]  
> 一般而言，本部分中的配置适用于关系数据库。 安装关系数据库提供程序时，此处显示的扩展方法将变为可用（原因在于共享的 Microsoft.EntityFrameworkCore.Relational 包）。

为模型中的每个备用键引入唯一约束。

## <a name="conventions"></a>约定

按照约定，为备用键引入的索引和约束将命名为`AK_<type name>_<property name>`。 对于复合备用键`<property name>` ，将变成以下划线分隔的属性名称列表。

## <a name="data-annotations"></a>数据注释

不能使用数据批注配置 Unique 约束。

## <a name="fluent-api"></a>Fluent API

您可以使用熟知的 API 来配置备用密钥的索引和约束名称。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/AlternateKeyName.cs?name=Model&highlight=9)]
