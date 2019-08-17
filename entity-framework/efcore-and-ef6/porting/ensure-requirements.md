---
title: 从 EF6 移植到 EF Core-验证要求
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: d3b66f3c-9d10-4974-a090-8ad093c9a53d
uid: efcore-and-ef6/porting/ensure-requirements
ms.openlocfilehash: 07caa39e8a56db71e493331ea9f0286309abc52a
ms.sourcegitcommit: 7b7f774a5966b20d2aed5435a672a1edbe73b6fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2019
ms.locfileid: "69565347"
---
# <a name="before-porting-from-ef6-to-ef-core-validate-your-applications-requirements"></a>从 EF6 迁移到 EF Core 之前:验证应用程序的要求

在开始迁移过程之前很重要，以验证 EF Core满足你的应用程序的数据访问要求。

## <a name="missing-features"></a>缺少功能

请确保 EF Core具有所需应用程序中使用的所有功能。 请参阅[功能比较](../features.md)有关如何在 EF Core中设置的功能比较到 ef6 更高版本的详细比较。 如果缺少任何所需的功能，请确保，你可以补偿缺少这些功能移植到 EF Core之前。

## <a name="behavior-changes"></a>行为更改

这是从 EF6 和 EF Core之间的行为中的某些更改非详尽列表。 请务必记住这几点你端口作为你的应用程序因为它们可能更改你的应用程序的行为，但将不显示为编译错误后交换到 EF Core的方式。

### <a name="dbsetaddattach-and-graph-behavior"></a>DbSet 添加/附加和图形行为

在 EF6 中, `DbSet.Add()`对实体调用会导致递归搜索其导航属性中引用的所有实体。 找到并且尚未由上下文跟踪的任何实体也将标记为已添加。 `DbSet.Attach()`的行为相同, 只不过所有实体都标记为不变。

**EF Core执行类似的递归搜索，但使用一些略有不同的规则。**

*  根实体始终处于请求状态 (在中为添加`DbSet.Add` , 对于为`DbSet.Attach`不更改)。

*  **对于在递归搜索导航属性期间找到的实体:**

    *  **如果实体的主键是已生成的存储**

        * 如果主键未设置为值, 则将状态设置为 "已添加"。 如果将主键值分配给属性类型的 CLR `0`默认值 (例如`int` `null` `string`, 对于、、等), 则将其视为 "未设置"。

        * 如果主键设置为值, 则状态将设置为 "未更改"。

    *  如果主键不是数据库生成的, 则将实体置于与根相同的状态。

### <a name="code-first-database-initialization"></a>Code First 数据库初始化

**EF6 在选择数据库连接和初始化数据库方面所执行的神奇的神奇。其中一些规则包括:**

* 如果未执行任何配置, EF6 将选择 SQL Express 或 LocalDb 上的数据库。

* 如果应用程序`App/Web.config`文件中包含与上下文名称相同的连接字符串, 则将使用此连接。

* 如果数据库不存在, 则创建它。

* 如果数据库中不存在来自模型的任何表, 则会将当前模型的架构添加到数据库中。 如果已启用迁移, 则会使用它们来创建数据库。

* 如果数据库存在并且 EF6 以前创建了架构, 则检查架构是否与当前模型兼容。 如果自创建架构以来模型发生了更改, 则会引发异常。

**EF Core不执行任何此幻数。**

* 必须在代码中显式配置数据库连接。

* 不执行初始化。 必须使用`DbContext.Database.Migrate()`来应用迁移 (或`DbContext.Database.EnsureCreated()`和, `EnsureDeleted()`以便在不使用迁移的情况下创建/删除数据库)。

### <a name="code-first-table-naming-convention"></a>Code First 表命名约定

EF6 通过复数形式服务运行实体类名称, 以计算实体映射到的默认表名称。

EF Core使用的名称的`DbSet`派生上下文公开了实体的属性。 如果该实体不具有`DbSet`属性, 则使用类名称。
