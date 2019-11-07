---
title: 主键-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c78f8f42-564a-45a4-aca7-3ede9f7ed2bc
uid: core/modeling/relational/primary-keys
ms.openlocfilehash: c195cc37859ea0689b5c6fbb84051564cf96c83a
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656053"
---
# <a name="primary-keys"></a>主键

> [!NOTE]  
> 一般而言，本部分中的配置适用于关系数据库。 安装关系数据库提供程序时，此处显示的扩展方法将变为可用（原因在于共享的 Microsoft.EntityFrameworkCore.Relational 包）。

为每个实体类型的键引入 primary key 约束。

## <a name="conventions"></a>约定

按照约定，会将数据库中的主键命名为 `PK_<type name>`。

## <a name="data-annotations"></a>数据注释

不能使用数据批注配置主键的关系数据库的特定方面。

## <a name="fluent-api"></a>Fluent API

您可以使用 "熟知 API" 在数据库中配置 primary key 约束的名称。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/KeyName.cs?name=KeyName&highlight=9)]
