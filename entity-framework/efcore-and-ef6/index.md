---
title: 比较 EF6 和 EF Core
description: 有关如何在 EF6 与 EF Core 之间进行选择的指导。
author: ajcvickers
ms.date: 01/23/2019
ms.assetid: a6b9cd22-6803-4c6c-a4d4-21147c0a81cb
uid: efcore-and-ef6/index
ms.openlocfilehash: 8e99cd9863d9f206ef3ae4d68226283019db835f
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619750"
---
# <a name="compare-ef-core--ef6"></a>比较 EF Core 和 EF6

## <a name="ef-core"></a>EF Core

Entity Framework Core ([EF Core](xref:core/index)) 是适用于 .NET 的新式对象数据库映射器。 它支持 LINQ 查询、更改跟踪、更新和架构迁移。

EF Core 通过[数据库提供程序插件模型](xref:core/providers/index)与 SQL Server/SQL Azure、SQLite、Azure Cosmos DB、MySQL、PostgreSQL 和更多数据库配合使用。

## <a name="ef6"></a>EF6

Entity Framework 6 ([EF6](xref:ef6/index)) 是专为 .NET Framework 设计的对象关系映射器，但支持 .NET Core。 EF6 是一款受支持的稳定产品，但我们不再对其进行积极开发。

## <a name="feature-comparison"></a>功能比较

EF Core 提供了不会在 EF6 中实现的新功能。 但是，并非所有 EF6 功能都已在 EF Core 中实现。

下表比较了 EF Core 和 EF6 中可用的功能。 这只是大致比较，没有列出全部功能，也未解释不同 EF 版本中相同功能之间的差异。

EF Core 列指出了功能首次出现的产品版本。

### <a name="creating-a-model"></a>创建模型

| **功能**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| 基本类映射                                   | 是      | 1.0                                   |
| 带有参数的构造函数                          |          | 2.1                                   |
| 属性值转换                            |          | 2.1                                   |
| 没有键的映射类型                             |          | 2.1                                   |
| 约定                                           | 是      | 1.0                                   |
| 自定义约定                                    | 是      | 1.0（部分；[#214](https://github.com/dotnet/efcore/issues/214)） |
| 数据注释                                      | 是      | 1.0                                   |
| Fluent API                                            | 是      | 1.0                                   |
| 继承：每个层次结构一张表 (TPH)                | 是      | 1.0                                   |
| 继承：每个类型一张表 (TPT)                     | 是      | 计划在 5.0 版中推出 ([#2266](https://github.com/dotnet/efcore/issues/2266)) |
| 继承：每个具体类一张表 (TPC)           | 是      | 5.0 版的延伸目标 ([#3170](https://github.com/dotnet/efcore/issues/3170)) <sup>(1)</sup> |
| 阴影状态属性                               |          | 1.0                                   |
| 备用键                                        |          | 1.0                                   |
| 多对多导航                              | 是      | 计划在 5.0 版中推出 ([#19003](https://github.com/dotnet/efcore/issues/19003)) |
| 多对多，无联接实体                      | 是      | 积压工作 ([#1368](https://github.com/dotnet/efcore/issues/1368)) |
| 密钥生成：数据库                              | 是      | 1.0                                   |
| 密钥生成：客户端                                |          | 1.0                                   |
| 复杂/已拥有类型                                   | 是      | 2.0                                   |
| 空间数据                                          | 是      | 2.2                                   |
| 模型格式：代码                                    | 是      | 1.0                                   |
| 从数据库创建模型：命令行              | 是      | 1.0                                   |
| 从数据库更新模型                            | 部分  | 积压工作 ([#831](https://github.com/dotnet/efcore/issues/831)) |
| 全局查询筛选器                                  |          | 2.0                                   |
| 表拆分                                       | 是      | 2.0                                   |
| 实体拆分                                      | 是      | 5.0 版的延伸目标 ([#620](https://github.com/dotnet/efcore/issues/620)) <sup>(1)</sup> |
| 数据库标量函数映射                      | 差     | 2.0                                   |
| 字段映射                                         |          | 1.1                                   |
| 可为空引用类型 (C# 8.0)                     |          | 3.0                                   |
| 模型的图形可视化效果                      | 是      | 未计划支持 <sup>(2)</sup>     |
| 图形模型编辑器                                | 是      | 未计划支持 <sup>(2)</sup>     |
| 模型格式：EDMX (XML)                              | 是      | 未计划支持 <sup>(2)</sup>     |
| 从数据库创建模型：VS 向导                 | 是      | 未计划支持 <sup>(2)</sup>     |

### <a name="querying-data"></a>正在查询数据

| **功能**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| LINQ 查询                                          | 是      | 1.0                                   |
| 可读内容生成的 SQL                                | 差     | 1.0                                   |
| GroupBy 转换                                   | 是      | 2.1                                   |
| 加载相关数据：预先加载                           | 是      | 1.0                                   |
| 加载相关数据：预先加载派生类型 |          | 2.1                                   |
| 加载相关数据：延迟加载                            | 是      | 2.1                                   |
| 加载相关数据：显式加载                        | 是      | 1.1                                   |
| 原始 SQL 查询：实体类型                         | 是      | 1.0                                   |
| 原生 SQL 查询：无键实体类型                 | 是      | 2.1                                   |
| 原始 SQL 查询：使用 LINQ 编写                  |          | 1.0                                   |
| 显式编译的查询                           | 差     | 2.0                                   |
| await foreach (C# 8.0)                                |          | 3.0                                   |
| 基于文本的查询语言（实体 SQL）                | 是      | 未计划支持 <sup>(2)</sup>     |

### <a name="saving-data"></a>保存数据

| **功能**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| 更改跟踪：快照                             | 是      | 1.0                                   |
| 更改追踪：通知                         | 是      | 1.0                                   |
| 更改跟踪：代理                              | 是      | 已在 5.0 版中合并 ([#10949](https://github.com/dotnet/efcore/issues/10949)) |
| 访问跟踪的状态                               | 是      | 1.0                                   |
| 开放式并发                                | 是      | 1.0                                   |
| 事务                                          | 是      | 1.0                                   |
| 批处理语句                                |          | 1.0                                   |
| 存储过程映射                              | 是      | 积压工作 ([#245](https://github.com/dotnet/efcore/issues/245)) |
| 断开连接低级别 API 图形                     | 差     | 1.0                                   |
| 断开连接端到端图形                         |          | 1.0（部分；[#5536](https://github.com/dotnet/efcore/issues/5536)） |

### <a name="other-features"></a>其他功能

| **功能**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| 迁移                                            | 是      | 1.0                                   |
| 数据库创建/删除 API                       | 是      | 1.0                                   |
| 种子数据                                             | 是      | 2.1                                   |
| 连接复原                                 | 是      | 1.1                                   |
| 拦截器                                          | 是      | 3.0                                   |
| 事件                                                | 是      | 3.0（部分；[#626](https://github.com/dotnet/efcore/issues/626)） |
| 简单的日志记录 (Database.Log)                         | 是      | 已在 5.0 版中合并 ([#1199](https://github.com/dotnet/efcore/issues/1199)) |
| DbContext 池                                     |          | 2.0                                   |

### <a name="database-providers-sup3sup"></a>数据库提供程序<sup>(3)</sup>

| **功能**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| SQL Server                                            | 是      | 1.0                                   |
| MySQL                                                 | 是      | 1.0                                   |
| PostgreSQL                                            | 是      | 1.0                                   |
| Oracle                                                | 是      | 1.0                                   |
| SQLite                                                | 是      | 1.0                                   |
| SQL Server Compact                                    | 是      | 1.0 <sup>(4)</sup>                    |
| DB2                                                   | 是      | 1.0                                   |
| Firebird                                              | 是      | 2.0                                   |
| Jet (Microsoft Access)                                |          | 2.0 <sup>(4)</sup>                    |
| Azure Cosmos DB                                       |          | 3.0                                   |
| 内存中（用于测试）                               |          | 1.0                                   |

<sup>1</sup> 在给定版本中，不太可能实现延伸目标。 但如果一切顺利，我们将尝试加入相关功能。

<sup>2</sup> EF Core 中不会实现某些 EF6 功能。 这些功能依赖于 EF6 的基础实体数据模型 (EDM)，并且/或者是复杂功能，投资回报率相对较低。 欢迎提出反馈，但是，尽管 EF Core 支持许多在 EF6 中无法实现的功能，反过来，EF Core 支持 EF6 的所有功能却并不可行。

<sup>3</sup> 更新到新的 EF Core 主版本时，第三方实现的 EF Core 数据库提供程序可能延迟。 有关详细信息，请参阅[数据库提供程序](xref:core/providers/index)。

<sup>4</sup> SQL Server Compact 和 Jet 提供程序仅适用于 .NET Framework（而不适用于 .NET Core）。

### <a name="supported-platforms"></a>受支持的平台

EF Core 3.1 通过使用 .NET Standard 2.0 在 .NET Core 和 .NET Framework 上运行。 但 EF Core 5.0 不会在 .NET Framework 上运行。 有关更多详细信息，请参阅[平台](xref:core/platforms/index)。

EF6.4 通过多目标在 .NET Core 和 .NET Framework 上运行。

## <a name="guidance-for-new-applications"></a>针对新应用程序的选择指南

除非应用需要[仅在 .NET Framework 上受支持](/dotnet/standard/choosing-core-framework-server)的内容，否则对于所有新应用程序都在 .NET Core 上使用 EF Core。

## <a name="guidance-for-existing-ef6-applications"></a>针对现有 EF6 应用程序的选择指南

EF Core 不是 EF6 的直接替换项。 从 EF6 迁移到 EF Core 可能需要更改应用程序。

将 EF6 应用迁移到 .NET Core 时：

* 如果数据访问代码稳定且不太可能开发或需要新功能，请继续使用 EF6。
* 如果数据访问代码不断演变，或应用需要仅在 EF Core 中提供的新功能，请迁移到 EF Core。
* 迁移到 EF Core 通常也是为了提高性能。 但是，并非所有方案都可提高性能，因此请先进行分析。

有关详细信息，请参阅[从 EF6 到 EF Core 的迁移](xref:efcore-and-ef6/porting/index)。
