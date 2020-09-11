---
title: 管理数据库架构 - EF Core
description: 使用 Entity Framework Core 管理数据库架构的策略的概述
author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/index
ms.openlocfilehash: e4f8c82125534e9e7e0c2de552bce336a544a2aa
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619497"
---
# <a name="managing-database-schemas"></a>管理数据库架构

EF Core 提供两种主要方法来保持 EF Core 模型和数据库架构同步。至于我们应该选用哪个方法，请确定你是希望以 EF Core 模型为准还是以数据库为准。

如果希望以 EF Core 模型为准，请使用[迁移][1]。 对 EF Core 模型进行更改时，此方法会以增量方式将相应架构更改应用到数据库，以使数据库保持与 EF Core 模型兼容。

如果希望以数据库架构为准，请使用[反向工程][2]。 使用此方法，可通过将数据库架构反向工程到 EF Core 模型来生成相应的 DbContext 和实体类型。

> [!NOTE]
> [创建和删除 API][3] 也可从 EF Core 模型创建数据库架构。 但是，它们主要适用于允许删除数据库的场景，比如测试、原型制作等。

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/managing-schemas/scaffolding
  [3]: xref:core/managing-schemas/ensure-created
