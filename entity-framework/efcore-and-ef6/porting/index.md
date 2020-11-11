---
title: 从 EF6 移植到 EF Core - EF
description: 有关将应用程序从 Entity Framework 6 移植到 Entity Framework Core 的常规信息
author: ajcvickers
ms.date: 10/27/2016
uid: efcore-and-ef6/porting/index
ms.openlocfilehash: d50def47e65455c8cf5242cad4386f157148c0bc
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429203"
---
# <a name="porting-from-ef6-to-ef-core"></a>从 EF6 移植到 EF Core

由于 EF Core 有一些根本性变化，我们不建议尝试将 EF6 应用程序迁移至 EF Core，除非你有令人信服的理由进行此项更改。
你应该将从 EF6 移至 EF Core 视作移植而不是升级。

> [!IMPORTANT]
> 启动移植过程前，务必验证 EF Core 符合应用程序的数据访问要求。

## <a name="missing-features"></a>缺少的功能

请确保 EF Core 包含需要在应用程序中使用的所有功能。 有关 EF Core 与 EF6 中功能集的详细比较，请参阅[功能比较](xref:efcore-and-ef6/index)。 若缺少所需的任何功能，请确保在移植到 EF Core 前弥补这些功能的缺失。

## <a name="behavior-changes"></a>行为更改

下表包含 EF6 和 EF Core 之间的一些行为变更，非完整列表。 移植应用程序时，务必牢记它们，因为这些可能会更改应用程序的行为方式，而在交换到 EF Core 后它们不会显示为编译错误。

### <a name="dbsetaddattach-and-graph-behavior"></a>DbSet.Add/Attach 和图形行为

在 EF6 中，在实体上调用 `DbSet.Add()` 将递归搜索导航属性引用的所有实体。 所找到的上下文未在跟踪的任何实体也会被标记为“已添加”。 `DbSet.Attach()` 的行为相同，但会将所有实体标记为“未更改”。

**EF Core 执行相似的递归搜索，但有些规则略有不同。**

* 根实体始终处于请求的状态（对于 `DbSet.Add` 为“已添加”，对于 `DbSet.Attach` 为“未更改”）。
* **适用于递归搜索导航属性期间找到的所有实体：**
  * **若实体的主键由存储生成**
    * 若主键未设置为值，状态将设置为“已添加”。 若为主键值的属性类型分配了 CLR 默认值（如为 `int` 分配 `0`，为 `string` 分配 `null` 等），则视为“未设置”它。
    * 若主键设置为值，状态将设置为“未更改”。
  * 若主键非由数据库生成，则将实体置于与根相同的状态。

### <a name="code-first-database-initialization"></a>Code First 数据库初始化

**EF6 包含许多关于执行数据库连接和数据库初始化的功能。这些规则包括：**

* 若未执行任何配置，EF6 将从 SQL Express 或 LocalDb 选择数据库。

* 若应用程序 `App/Web.config` 文件包含与上下文名称相同的连接字符串，将使用此连接。

* 如果数据库不存在，则会创建一个。

* 若数据库中不存在模型中的任何表，则会将当前模型的架构添加到数据库。 若启用了迁移，则会使用它们创建数据库。

* 若数据库存在，并且 EF6 此前已创建架构，则将检查架构是否与当前模型兼容。 若在创建架构后模型已更改，将引发异常。

**EF Core 不执行任何此类功能。**

* 必须在代码中显式配置数据库连接。

* 不执行初始化。 必须使用 `DbContext.Database.Migrate()` 应用迁移（或使用 `DbContext.Database.EnsureCreated()`/`EnsureDeleted()` 创建/删除数据库，而不使用迁移）。

### <a name="code-first-table-naming-convention"></a>Code First 表命名约定

EF6 通过复数形式服务运行实体类名，来评估实体映射到的默认表名称。

EF Core 使用派生的上下文上公开实体的 `DbSet` 属性的名称。 若实体不包含 `DbSet` 属性，则使用类名。
