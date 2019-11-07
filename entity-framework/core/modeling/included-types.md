---
title: 包括类型 & （包括类型）-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: cbe6935e-2679-4b77-8914-a8d772240cf1
uid: core/modeling/included-types
ms.openlocfilehash: 1249e71c02e58afe7fe06b3fdcf523dfa0c9b17c
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655740"
---
# <a name="including--excluding-types"></a>包含类型和排除类型

如果在模型中包含类型，则意味着 EF 具有与该类型相关的元数据，并将尝试在数据库中读取和写入实例。

## <a name="conventions"></a>约定

按照约定，你的上下文中 `DbSet` 属性中公开的类型将包含在你的模型中。 此外，还包括 `OnModelCreating` 方法中提到的类型。 最后，通过递归浏览已发现类型的导航属性找到的所有类型也包含在该模型中。

**例如，在以下代码中，将发现所有三种类型：**

* `Blog`，因为它在上下文的 `DbSet` 属性中公开

* `Post`，因为它是通过 `Blog.Posts` 导航属性发现的

* `AuditEntry`，因为 `OnModelCreating` 中提到了它

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/IncludedTypes.cs?name=IncludedTypes&highlight=3,7,16)]

## <a name="data-annotations"></a>数据注释

您可以使用数据批注从模型中排除类型。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreType.cs?highlight=20)]

## <a name="fluent-api"></a>Fluent API

您可以使用熟知的 API 从模型中排除类型。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreType.cs?highlight=12)]
