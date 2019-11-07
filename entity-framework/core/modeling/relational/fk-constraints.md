---
title: Foreign Key 约束-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: dbaf4bac-1fd5-46c0-ac57-64d7153bc574
uid: core/modeling/relational/fk-constraints
ms.openlocfilehash: df739f01a799ec8edad4cf44d8cf50edf292992f
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656001"
---
# <a name="foreign-key-constraints"></a>外键约束

> [!NOTE]  
> 一般而言，本部分中的配置适用于关系数据库。 安装关系数据库提供程序时，此处显示的扩展方法将变为可用（原因在于共享的 Microsoft.EntityFrameworkCore.Relational 包）。

为模型中的每个关系引入了 foreign key 约束。

## <a name="conventions"></a>约定

按照约定，外键约束命名 `FK_<dependent type name>_<principal type name>_<foreign key property name>`。 对于复合外键 `<foreign key property name>` 成为外键属性名称的以下划线分隔的列表。

## <a name="data-annotations"></a>数据注释

不能使用数据批注配置 Foreign key 约束名称。

## <a name="fluent-api"></a>Fluent API

您可以使用熟知的 API 来配置关系的外键约束名称。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/RelationshipConstraintName.cs?name=Constraint&highlight=12)]
