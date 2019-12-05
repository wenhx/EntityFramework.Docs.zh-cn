---
title: Foreign Key 约束-EF Core
author: AndriySvyryd
ms.date: 11/21/2019
uid: core/modeling/relational/fk-constraints
ms.openlocfilehash: 2855137adf2ba3c9edaabd15a05f7a209f00f685
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824591"
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
