---
title: Entity Framework Core 概述 - EF Core
description: Entity Framework Core 的一般介绍概述
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: bc2a2676-bc46-493f-bf49-e3cc97994d57
uid: core/index
ms.openlocfilehash: 4816f2d57590ddd2ad4807daa2cc1c5000cd5713
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619482"
---
# <a name="entity-framework-core"></a>Entity Framework Core

Entity Framework (EF) Core 是轻量化、可扩展、[开源](https://github.com/aspnet/EntityFrameworkCore)和跨平台版的常用 Entity Framework 数据访问技术。

EF Core 可用作对象关系映射程序 (O/RM)，以便于 .NET 开发人员能够使用 .NET 对象来处理数据库，这样就不必经常编写大部分数据访问代码了。

EF Core 支持多个数据库引擎，请参阅[数据库提供程序](xref:core/providers/index)了解详细信息。

## <a name="the-model"></a>模型

对于 EF Core，使用模型执行数据访问。 模型由实体类和表示数据库会话的上下文对象构成，可便于用户查询和保存数据。 有关详细信息，请参阅[创建模型](xref:core/modeling/index)。

可以根据现有数据库生成模型，手动将模型编码为与数据库匹配，也可以使用 [EF 迁移](xref:core/managing-schemas/migrations/index)根据模型创建数据库，然后在模型随时间推移发生更改时改进它。

[!code-csharp[Main](../../samples/core/Intro/Model.cs)]

## <a name="querying"></a>查询

使用语言集成查询 (LINQ) 从数据库检索实体类的实例。 有关详细信息，请参阅[查询数据](xref:core/querying/index)。

[!code-csharp[Main](../../samples/core/Intro/Program.cs#Querying)]

## <a name="saving-data"></a>保存数据

使用实体类的实例在数据库中创建、删除和修改数据。 有关详细信息，请参阅[保存数据](xref:core/saving/index)。

[!code-csharp[Main](../../samples/core/Intro/Program.cs#SavingData)]

## <a name="next-steps"></a>后续步骤

有关介绍性教程，请参阅 [Entity Framework Core 入门](xref:core/get-started/index)。
