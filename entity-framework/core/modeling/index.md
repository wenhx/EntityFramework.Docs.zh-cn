---
title: 创建并配置模型 - EF Core
description: 使用 Entity Framework Core 创建并配置模型的概述
author: rowanmiller
ms.date: 11/05/2019
uid: core/modeling/index
ms.openlocfilehash: 1380ed0c1af0fdd3ae24de311bea455cb57a7dcd
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071415"
---
# <a name="creating-and-configuring-a-model"></a>创建并配置模型

Entity Framework 使用一组约定基于实体类的形状构建模型。 可指定其他配置以补充和/或替代约定的内容。

本文介绍可应用于面向任何数据存储的模型的配置，以及面向任意关系数据库时可应用的配置。 提供程序还可支持特定于具体数据存储的配置。 有关提供程序特定配置的文档，请参阅 [数据库提供程序](xref:core/providers/index) 部分。

> [!TIP]  
> 可在 GitHub 上查看此文章的 [示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples) 。

## <a name="use-fluent-api-to-configure-a-model"></a>使用 fluent API 配置模型

可在派生上下文中覆写 `OnModelCreating` 方法，并使用 `ModelBuilder API` 来配置模型。 此配置方法最为有效，并可在不修改实体类的情况下指定配置。 Fluent API 配置具有最高优先级，并将替代约定和数据注释。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?highlight=12-14)]

## <a name="use-data-annotations-to-configure-a-model"></a>使用数据注释来配置模型

也可将特性（称为数据注释）应用于类和属性。 数据注释会替代约定，但会被 Fluent API 配置替代。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?highlight=15)]
