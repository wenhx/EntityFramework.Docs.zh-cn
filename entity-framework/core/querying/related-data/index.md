---
title: 加载相关数据 - EF Core
description: 使用 Entity Framework Core 加载相关数据的不同策略
author: roji
ms.date: 9/11/2020
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
uid: core/querying/related-data
ms.openlocfilehash: 5c1addac6735de39d5c72f447d5d06280411a21e
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90078848"
---
# <a name="loading-related-data"></a>加载相关数据

Entity Framework Core 允许你在模型中使用导航属性来加载相关实体。 有三种常见的 O/RM 模式可用于加载关联数据。

* [预先加载](xref:core/querying/related-data/eager)表示从数据库中加载关联数据，作为初始查询的一部分。
* [显式加载](xref:core/querying/related-data/explicit)表示稍后从数据库中显式加载关联数据。
* [延迟加载](xref:core/querying/related-data/lazy)表示在访问导航属性时，从数据库中以透明方式加载关联数据。

> [!TIP]
> 可以在 GitHub 上的此部分中查看[示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying)。
