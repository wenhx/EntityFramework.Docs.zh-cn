---
title: 开始使用 Entity Framework 6 - EF6
description: 开始使用 Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/get-started
ms.openlocfilehash: b518b8ec70045066ffce6ac2a32136df97e14e99
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062784"
---
# <a name="get-started-with-entity-framework-6"></a>开始使用 Entity Framework 6

本指南包含指向精选文章、演练和视频的链接集合，这些内容可帮助你快速入门。

## <a name="fundamentals"></a>基础知识

* [获取实体框架](xref:ef6/fundamentals/install)

  此处可学习如何将 Entity Framework 添加到应用程序。如果要使用 EF 设计器，请确保在 Visual Studio 中安装它。

* [创建模型：Code First、EF 设计器和 EF 工作流](xref:ef6/modeling/index)

  是否希望指定 EF 模型编写代码或绘制方框和线条？
是否要使用 EF 来将对象映射到现有数据库，或希望 EF 创建为对象量身打造的数据库？
本文介绍了使用 EF6 的两种不同方法：EF 设计器和 Code First。
请确保关注讨论内容并查看有关不同之处的视频。

* [使用 DbContext](xref:ef6/fundamentals/working-with-dbcontext)

  DbContext 是需要学习其使用方法的第一个也是最重要的一个 EF 类型。 它可用作数据库查询的启动板，并可跟踪对对象作出的更改，以便持续存回数据库。

* [提出问题](xref:ef6/resources/get-help)

  了解如何获取专家的帮助，并向社区贡献自己的答案。

* [参与](https://github.com/aspnet/EntityFramework6/)

  Entity Framework 6 采用开放式开发模型。 访问我们的 GitHub 存储库，了解如何帮助改进 EF。

## <a name="code-first-resources"></a>Code First 资源

  - [对现有数据库工作流采用 Code First](xref:ef6/modeling/code-first/workflows/existing-database)
  - [对新的数据库工作流采用 Code First](xref:ef6/modeling/code-first/workflows/new-database)
  - [使用 Code First 映射枚举](xref:ef6/modeling/code-first/data-types/enums)
  - [使用 Code First 映射空间类型](xref:ef6/modeling/code-first/data-types/spatial)
  - [编写自定义 Code First 约定](xref:ef6/modeling/code-first/conventions/custom)
  - [将 Code First Fluent 配置与 Visual Basic 配合使用](xref:ef6/modeling/code-first/fluent/vb)
  - [Code First 迁移](xref:ef6/modeling/code-first/migrations/index)
  - [团队环境中的 Code First 迁移](xref:ef6/modeling/code-first/migrations/teams)
  - [自动 Code First 迁移](xref:ef6/modeling/code-first/migrations/automatic)（不再推荐）

## <a name="ef-designer-resources"></a>EF 设计器资源
  - [Database First 工作流](xref:ef6/modeling/designer/workflows/database-first)
  - [Model First 工作流](xref:ef6/modeling/designer/workflows/model-first)
  - [映射枚举](xref:ef6/modeling/designer/data-types/enums)
  - [映射空间类型](xref:ef6/modeling/designer/data-types/spatial)
  - [每个层次结构一张表继承映射](xref:ef6/modeling/designer/inheritance/tph)
  - [每个类型一张表继承映射](xref:ef6/modeling/designer/inheritance/tpt)
  - [用于更新的存储过程映射](xref:ef6/modeling/designer/stored-procedures/cud)
  - [用于查询的存储过程映射](xref:ef6/modeling/designer/stored-procedures/query)
  - [实体拆分](xref:ef6/modeling/designer/entity-splitting)
  - [表拆分](xref:ef6/modeling/designer/table-splitting)
  - [定义查询](xref:ef6/modeling/designer/advanced/defining-query)（高级）
  - [表值函数](xref:ef6/modeling/designer/advanced/tvfs)（高级）

## <a name="other-resources"></a>其他资源
  - [异步查询和保存](xref:ef6/fundamentals/async)
  - [使用 WinForms 进行数据绑定](xref:ef6/fundamentals/databinding/winforms)
  - [使用 WPF 进行数据绑定](xref:ef6/fundamentals/databinding/wpf)
  - [使用自跟踪实体的断开连接的方案](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough)（不再推荐）
