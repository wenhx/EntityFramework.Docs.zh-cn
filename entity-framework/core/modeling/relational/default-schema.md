---
title: 默认架构-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e6e58473-9f5e-4a1f-ac0f-b87d2cbb667e
uid: core/modeling/relational/default-schema
ms.openlocfilehash: 1579fed007997aa4cf49b4c1290aee86c81c0000
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655972"
---
# <a name="default-schema"></a>默认架构

> [!NOTE]  
> 一般而言，本部分中的配置适用于关系数据库。 安装关系数据库提供程序时，此处显示的扩展方法将变为可用（原因在于共享的 Microsoft.EntityFrameworkCore.Relational 包）。

如果没有为该对象显式配置架构，则默认架构为将在其中创建对象的数据库架构。

## <a name="conventions"></a>约定

按照约定，数据库提供程序将选择最适合的默认架构。 例如，Microsoft SQL Server 将使用 `dbo` 架构，SQLite 将不使用架构（因为 SQLite 不支持架构）。

## <a name="data-annotations"></a>数据注释

不能使用数据批注设置默认架构。

## <a name="fluent-api"></a>Fluent API

可以使用 "熟知 API" 来指定默认架构。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/DefaultSchema.cs?name=DefaultSchema&highlight=7)]
