---
title: 实体框架的过去版本-EF6
author: divega
ms.date: 09/12/2019
ms.assetid: 1060bb99-765f-4f32-aaeb-d6635d3dbd3e
uid: ef6/what-is-new/past-releases
ms.openlocfilehash: fada7740453cd9a55a1d0069236efcecbd9aa314
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656141"
---
# <a name="past-releases-of-entity-framework"></a>实体框架的过去版本

第一版实体框架在2008中发布，作为 .NET Framework 3.5 SP1 和 Visual Studio 2008 SP1 的一部分。

从 EF 4.1 版本开始，该版本已作为[EntityFramework NuGet 包](https://www.nuget.org/packages/EntityFramework/)提供-当前 NuGet.org 上最热门的包之一。

在版本4.1 和5.0 之间，EntityFramework NuGet 包扩展了作为 .NET Framework 一部分提供的 EF 库。

从版本6开始，EF 变成了一个开源项目，并完全从带外移动了 .NET Framework。
这意味着，当你将 EntityFramework 版本 6 NuGet 包添加到应用程序时，你将获得 EF 库的完整副本，该副本不依赖于 .NET Framework 附带的 EF 位。
这有助于在一定程度上加快新功能的开发和交付速度。

2016年6月发布 EF Core 1.0。 EF Core 基于新的代码库，旨在作为 EF 的更轻型且可扩展的版本。
目前 EF Core 是 Microsoft 的实体框架团队开发的主要焦点。
这意味着没有为 EF6 计划的新的主要功能。 但是，EF6 仍将保持为开源项目和受支持的 Microsoft 产品。

下面是过去版本的列表，采用反向时间顺序，其中包含有关每个版本中引入的新功能的信息。

## <a name="ef-tools-update-in-visual-studio-2017-157"></a>Visual Studio 2017 15.7 中的 EF 工具更新
2018 年 5 月，我们在 Visual Studio 2017 15.7 中发布了更新后的 EF 工具。
该版本包括对一些常见点的改进：

- 修复了多个用户界面辅助功能 bug
- 从现有数据库生成模型时 SQL Server 性能退化的解决方法 [#4](https://github.com/aspnet/entityframework6/issues/4)
- 支持适用于 SQL Server 上较大模型的更新模型 [#185](https://github.com/aspnet/EntityFramework6/issues/185)

此新版本的 EF 工具中的另一项改进是，在新项目中创建模型时会安装 EF 6.2 运行时。 借助较旧版本的 Visual Studio，可通过安装相应版本的 NuGet 包来使用 EF 6.2 运行时（以及以前任何版本的 EF）。

## <a name="ef-620"></a>EF 6.2。0
EF 6.2 运行时已于 2017 年 10 月发布到 NuGet。
在我们开源社区参与者的努力下，EF 6.2 包括大量的 [bug 修复](https://github.com/aspnet/entityframework6/issues?utf8=%E2%9C%93&q=is%3Aissue%20milestone%3A6.2.0%20is%3Aclosed%20label%3Aclosed-fixed%20-label%3Aarea-tools%20label%3Atype-bug)和[产品增强功能](https://github.com/aspnet/entityframework6/issues?utf8=%E2%9C%93&q=is%3Aissue%20milestone%3A6.2.0%20is%3Aclosed%20label%3Aclosed-fixed%20-label%3Aarea-tools%20label%3Atype-enhancement%20)。

下表简要列出了影响 EF 6.2 运行时的最重要的更改：

- 通过加载持久性缓存中已完成的 Code First 模型来加快启动 [#275](https://github.com/aspnet/EntityFramework6/issues/275)
- 采用 Fluent API 定义索引 [#274](https://github.com/aspnet/EntityFramework6/issues/274)
- 通过 DbFunctions.Like() 编写在 SQL 中转换为 LIKE 的 LINQ 查询 [#241](https://github.com/aspnet/EntityFramework6/issues/241)
- Migrate.exe 现支持脚本选项 [#240](https://github.com/aspnet/EntityFramework6/issues/240)
- EF6 现在可使用 SQL Server 中的序列生成的键值 [#165](https://github.com/aspnet/EntityFramework6/issues/165)
- 更新 SQL Azure 执行策略的暂时性错误列表 [#83](https://github.com/aspnet/EntityFramework6/issues/83)
- Bug：重试查询或 SQL 命令失败，“另一 SqlParameterCollection 中已包含 SqlParameter”[#81](https://github.com/aspnet/EntityFramework6/issues/81)
- Bug：DbQuery.ToString() 评估在调试程序中经常超时 [#73](https://github.com/aspnet/EntityFramework6/issues/73)

## <a name="ef-613"></a>EF EF6.1。3
EF ef6.1.3 运行时发布到了2015年10月的 NuGet。
此版本仅包含对6.1.2 版本报告的高优先级缺陷和回归的修补程序。
修复包括：

- 查询：EF 6.1.2 中的回归：OUTER 适用于1:1 关系和 "let" 子句的已引入和更复杂的查询
- 继承类中隐藏基类属性的 TPT 问题
- 当文本中包含单词 "DbMigration" 时，Sql 失败
- 创建供 unionall 和交集平展支持的兼容性标志
- 具有多个包含的查询在6.1.2 中不起作用（在6.1.1 中工作）
- 从 EF 6.1.1 升级到6.1.2 后，出现 SQL 语法错误

## <a name="ef-612"></a>EF 6.1。2
EF 6.1.2 运行时已发布到2014年12月的 NuGet。
此版本主要涉及 bug 修复。 我们还接受了社区成员的几个值得注意的更改：
- **可以从应用程序/web.config 文件配置查询缓存参数**
    ``` xml
    <entityFramework>
      <queryCache size='1000' cleaningIntervalInSeconds='-1'/>
    </entityFramework>
    ```
- **DbMigration 上的 SqlFile 和 SqlResource 方法**允许您运行存储为文件或嵌入资源的 SQL 脚本。

## <a name="ef-611"></a>EF 6.1。1
EF 6.1.1 运行时已发布到2014年6月。
此版本包含许多人遇到的问题的修补程序。 其他：
- @在 EF6 设计器中打开具有十进制精度的 EF5 edmx 时出错
- LocalDB 的默认实例检测逻辑不适用于 SQL Server 2014

## <a name="ef-610"></a>EF 6.1。0
EF 6.1.0 运行时发布到了2014年3月的 NuGet。
此次要更新包含大量新功能：

- **工具合并**为创建新的 EF 模型提供了一种一致的方法。 此功能[扩展了 ADO.NET 实体数据模型向导以支持创建 Code First 模型](~/ef6/modeling/code-first/workflows/existing-database.md)，包括从现有数据库进行反向工程。 这些功能之前已在 EF Power Tools 中提供 Beta 版质量。
- **[处理事务提交失败](~/ef6/fundamentals/connection-resiliency/commit-failures.md)** 会提供 CommitFailureHandler，它利用新引入的截取事务操作的能力。 CommitFailureHandler 允许在提交事务的同时从连接故障中自动恢复。
- **[IndexAttribute](~/ef6/modeling/code-first/data-annotations.md)** 允许通过在 Code First 模型中的属性（或属性）上放置 `[Index]` 属性来指定索引。 然后 Code First 将在数据库中创建相应的索引。
- **公共映射 API**提供对信息 EF 的访问，以了解如何将属性和类型映射到数据库中的列和表。 在以前的版本中，此 API 是内部的。
- **[通过 App/web.config 文件配置侦听器的能力](~/ef6/fundamentals/configuring/config-file.md)** 允许添加侦听器，而无需重新编译应用程序。
- **DatabaseLogger**是一个新的侦听器，可让你轻松地将所有数据库操作记录到文件中。 与上一项功能结合使用，可以轻松地针对已[部署的应用程序的数据库操作进行日志记录](~/ef6/fundamentals/configuring/config-file.md)，而无需重新编译。
- 改进了**迁移模型更改检测**，使基架迁移更准确;还增强了更改检测过程的性能。
- **性能改进**，包括在初始化过程中减少数据库操作、在 LINQ 查询中优化 null 相等性比较、更快速地生成视图（创建模型）以及更高效地具体化具有多个关联的跟踪实体。

## <a name="ef-602"></a>EF 6.0。2
EF 6.0.2 运行时已发布到2013年12月的 NuGet。
此修补程序版本仅限于修复在 EF6 版本中引入的问题（自 EF5 以来性能/行为的回归）。

## <a name="ef-601"></a>EF 6.0。1
EF 6.0.1 运行时已发布到2013年10月的 NuGet，因为后者嵌入到了几个月之前的 Visual Studio 版本中。
此修补程序版本仅限于修复在 EF6 版本中引入的问题（自 EF5 以来性能/行为的回归）。
最值得注意的更改是解决 EF 模型的预热过程中的某些性能问题。
这一点非常重要，因为预热性能是 EF6 的一个重点领域，而这些问题取消 EF6 的一些其他性能。

## <a name="ef-60"></a>EF 6。0
EF 6.0.0 运行时发布到了2013年10月的 NuGet。
这是[EntityFramework NuGet 包](https://www.nuget.org/packages/EntityFramework/)中的第一个版本，在该版本中，不依赖于 .NET Framework 中的 EF 位。
将运行时的剩余部分移到 NuGet 包需要对现有代码进行大量的重大更改。
有关升级所需的手动步骤的详细信息，请参阅[升级到实体框架 6](upgrading-to-ef6.md)部分。

此版本包含许多新功能。
以下功能适用于使用 Code First 或 EF 设计器创建的模型：

- **[异步查询和保存](~/ef6/fundamentals/async.md)** 增加了对 .net 4.5 中引入的基于任务的异步模式的支持。
- **[连接复原](~/ef6/fundamentals/connection-resiliency/retry-logic.md)** 允许从暂时性连接故障中自动恢复。
- **[基于代码的配置](~/ef6/fundamentals/configuring/code-based.md)** 使你能够在代码中执行配置（传统上在配置文件中执行）。
- **[依赖项解析](~/ef6/fundamentals/configuring/dependency-resolution.md)** 引入了对服务定位器模式的支持，并分解了某些功能，这些功能可以替换为自定义实现。
- **[拦截/SQL 日志记录](~/ef6/fundamentals/logging-and-interception.md)** 提供低级别的构建基块，用于截获 EF 操作，同时构建简单的 SQL 日志记录。
- [使用模拟 framework](~/ef6/fundamentals/testing/mocking.md)或[编写你自己的测试](~/ef6/fundamentals/testing/writing-test-doubles.md)时，可测试性**改进**可以更轻松地为 DbContext 和 DbSet 创建测试双精度。
- **[现在可以使用已打开的 DbConnection 创建 DbContext，](~/ef6/fundamentals/connection-management.md)** 这会启用在创建上下文时可以打开连接的情况（例如共享组件之间的连接，你无法保证连接状态）。
- **[改进的事务支持](~/ef6/saving/transactions.md)** 为框架的外部事务提供支持，并改进了在框架中创建事务的方式。
- **.Net 4.0 上的枚举、空间和更好的性能**-通过将已在 .NET Framework 中的核心组件移到 EF NuGet 包中，我们现在可以提供枚举支持、空间数据类型和 .net 4.0 上 EF5 的性能改进。
- 可**枚举的性能得到改进。包含在 LINQ 查询中**。
- **缩短了预热时间（视图生成）** ，尤其是对于大型模型。
- 可**插入复数形式 &amp; Singularization 服务**。
- 现在支持实体类上**Equals 或 GetHashCode 的自定义实现**。
- **DbSet. AddRange/RemoveRange**提供一种优化的方式来添加或删除集中的多个实体。
- **DbChangeTracker HasChanges**提供一种简单有效的方法来查看是否有任何挂起的更改保存到数据库中。
- **SqlCeFunctions**提供与 SqlFunctions 等效的 SQL Compact。

以下功能仅适用于 Code First：

- **[自定义 Code First 约定](~/ef6/modeling/code-first/conventions/custom.md)** 允许编写自己的约定，以帮助避免重复配置。 我们提供了一个简单的轻型约定 API 以及一些更复杂的构建基块，使你能够创作更复杂的约定。
- 现在支持 **[Code First 映射到插入/更新/删除存储过程](~/ef6/modeling/code-first/fluent/cud-stored-procedures.md)** 。
- **[幂等迁移脚本](~/ef6/modeling/code-first/migrations/index.md)** 允许生成一个 SQL 脚本，该脚本可以将任何版本的数据库升级到最新版本。
- 可 **[配置的迁移历史记录表](~/ef6/modeling/code-first/migrations/history-customization.md)** 允许自定义迁移历史记录表的定义。 这对于需要适当的数据类型等的数据库提供程序特别有用，因为需要为迁移历史记录表指定这些数据类型才能正常工作。
- 当使用迁移时，或 Code First 自动为您创建数据库时，**每个数据库的多个上下文**将删除每个数据库的一个 Code First 模型的以前限制。
- **[DbModelBuilder](~/ef6/modeling/code-first/fluent/types-and-properties.md)** 是一个新的 Code First API，它允许在一个位置配置 Code First 模型的默认数据库架构。 以前 Code First 的默认架构已硬编码为 &quot;dbo&quot;，以及通过 ToTable API 配置表所属架构的唯一方法。
- 在将配置类与 Code First 熟知 API 一起使用时， **AddFromAssembly 方法**可让你轻松地添加在程序集中定义的所有配置类。
- **[自定义迁移操作](https://romiller.com/2013/02/27/ef6-writing-your-own-code-first-migration-operations/)** 使您能够添加要在基于代码的迁移中使用的其他操作。
- 对于使用 Code First 创建的数据库，**默认事务隔离级别将更改为 READ_COMMITTED_SNAPSHOT** ，从而实现更高的可伸缩性和更少的死锁。
- **实体和复杂类型现在可以是 nestedinside 类**。

## <a name="ef-50"></a>EF 5。0
2012年8月发布了 EF 5.0.0 运行时。
此版本引入了一些新功能，包括枚举支持、表值函数、空间数据类型和各种性能改进。

Visual Studio 2012 中的 Entity Framework Designer 还引入了对每个模型的多个关系图的支持、设计图面上的形状的颜色以及存储过程的批处理导入。

下面是专门为 EF 5 版本组合在一起的内容列表：

-   [EF 5 Release Post](https://blogs.msdn.com/b/adonet/archive/2012/08/15/ef5-released.aspx)
-   EF5 中的新增功能
    -   [Code First 中的枚举支持](~/ef6/modeling/code-first/data-types/enums.md)
    -   [EF 设计器中的枚举支持](~/ef6/modeling/designer/data-types/enums.md)
    -   [Code First 中的空间数据类型](~/ef6/modeling/code-first/data-types/spatial.md)
    -   [EF 设计器中的空间数据类型](~/ef6/modeling/designer/data-types/spatial.md)
    -   [提供程序对空间类型的支持](~/ef6/fundamentals/providers/spatial-support.md)
    -   [表值函数](~/ef6/modeling/designer/advanced/tvfs.md)
    -   [每个模型多个关系图](~/ef6/modeling/designer/multiple-diagrams.md)
-   设置模型
    -   [创建模型](~/ef6/modeling/index.md)
    -   [连接和模型](~/ef6/fundamentals/configuring/connection-strings.md)
    -   [性能注意事项](~/ef6/fundamentals/performance/perf-whitepaper.md)
    -   [使用 Microsoft SQL Azure](~/ef6/fundamentals/connection-resiliency/retry-logic.md)
    -   [配置文件设置](~/ef6/fundamentals/configuring/config-file.md)
    -   [术语表](~/ef6/resources/glossary.md)
    -   Code First
        -   [Code First 到新数据库（演练和视频）](~/ef6/modeling/code-first/workflows/new-database.md)
        -   [Code First 现有数据库（演练和视频）](~/ef6/modeling/code-first/workflows/existing-database.md)
        -   [约定](~/ef6/modeling/code-first/conventions/built-in.md)
        -   [数据注释](~/ef6/modeling/code-first/data-annotations.md)
        -   [熟知 API-配置/映射属性 & 类型](~/ef6/modeling/code-first/fluent/types-and-properties.md)
        -   [熟知 API 配置关系](~/ef6/modeling/code-first/fluent/relationships.md)
        -   [通过 VB.NET 的流畅 API](~/ef6/modeling/code-first/fluent/vb.md)
        -   [Code First 迁移](~/ef6/modeling/code-first/migrations/index.md)
        -   [自动 Code First 迁移](~/ef6/modeling/code-first/migrations/automatic.md)
        -   [Debug.exe](~/ef6/modeling/code-first/migrations/migrate-exe.md)
        -   [定义 Dbset](~/ef6/modeling/code-first/dbsets.md)
    -   EF 设计器
        -   [Model First （演练和视频）](~/ef6/modeling/designer/workflows/model-first.md)
        -   [Database First （演练和视频）](~/ef6/modeling/designer/workflows/database-first.md)
        -   [复杂类型](~/ef6/modeling/designer/data-types/complex-types.md)
        -   [关联/关系](~/ef6/modeling/designer/relationships.md)
        -   [TPT 继承模式](~/ef6/modeling/designer/inheritance/tpt.md)
        -   [TPH 继承模式](~/ef6/modeling/designer/inheritance/tph.md)
        -   [带有存储过程的查询](~/ef6/modeling/designer/stored-procedures/query.md)
        -   [包含多个结果集的存储过程](~/ef6/modeling/designer/advanced/multiple-result-sets.md)
        -   [插入、更新 & 删除与存储过程](~/ef6/modeling/designer/stored-procedures/cud.md)
        -   [将实体映射到多个表（实体拆分）](~/ef6/modeling/designer/entity-splitting.md)
        -   [将多个实体映射到一个表（表拆分）](~/ef6/modeling/designer/table-splitting.md)
        -   [定义查询](~/ef6/modeling/designer/advanced/defining-query.md)
        -   [代码生成模板](~/ef6/modeling/designer/codegen/index.md)
        -   [恢复到 ObjectContext](~/ef6/modeling/designer/codegen/legacy-objectcontext.md)
-   使用模型
    -   [使用 DbContext](~/ef6/fundamentals/working-with-dbcontext.md)
    -   [查询/查找实体](~/ef6/querying/index.md)
    -   [使用关系](~/ef6/fundamentals/relationships.md)
    -   [正在加载相关实体](~/ef6/querying/related-data.md)
    -   [使用本地数据](~/ef6/querying/local-data.md)
    -   [N 层应用程序](~/ef6/fundamentals/disconnected-entities/index.md)
    -   [原始 SQL 查询](~/ef6/querying/raw-sql.md)
    -   [开放式并发模式](~/ef6/saving/concurrency.md)
    -   [使用代理](~/ef6/fundamentals/proxies.md)
    -   [自动检测更改](~/ef6/saving/change-tracking/auto-detect-changes.md)
    -   [无跟踪查询](~/ef6/querying/no-tracking.md)
    -   [加载方法](~/ef6/querying/load-method.md)
    -   [添加/附加和实体状态](~/ef6/saving/change-tracking/entity-state.md)
    -   [使用属性值](~/ef6/saving/change-tracking/property-values.md)
    -   [与 WPF 的数据绑定（Windows Presentation Foundation）](~/ef6/fundamentals/databinding/wpf.md)
    -   [用 WinForms （Windows 窗体）进行数据绑定](~/ef6/fundamentals/databinding/winforms.md)

## <a name="ef-431"></a>EF 4.3。1
Ef 4.3.1 运行时在 EF 4.3.0 之后不久发布到 2012 NuGet。
此修补版本包含对 EF 4.3 版本的一些 bug 修复，并为使用 EF 4.3 与 Visual Studio 2012 的客户引入更好的 LocalDB 支持。

下面是专门为 EF 4.3.1 版本组合在一起的内容列表，为 EF 4.1 提供的大多数内容也适用于 EF 4.3：

-   [EF 4.3.1 Release 博客文章](https://blogs.msdn.com/b/adonet/archive/2012/02/29/ef4-3-1-and-ef5-beta-1-available-on-nuget.aspx)

## <a name="ef-43"></a>EF 4。3
EF 4.3.0 运行时已发布到2012年2月的 NuGet。
此版本包含新的 Code First 迁移功能，该功能允许增量更改 Code First 创建的数据库，因为 Code First 模型演变。

下面是专为 EF 4.3 版本组合在一起的内容列表，为 EF 4.1 提供的大多数内容也适用于 EF 4.3：
-   [EF 4.3 发布后](https://blogs.msdn.com/b/adonet/archive/2012/02/09/ef-4-3-released.aspx)
-   [EF 4.3 基于代码的迁移演练](https://blogs.msdn.com/b/adonet/archive/2012/02/09/ef-4-3-code-based-migrations-walkthrough.aspx)
-   [EF 4.3 自动迁移演练](https://blogs.msdn.com/b/adonet/archive/2012/02/09/ef-4-3-automatic-migrations-walkthrough.aspx)

## <a name="ef-42"></a>EF 4。2
EF 4.2.0 运行时已发布到2011年11月的 NuGet。
此版本包含对 EF 4.1.1 版本的 bug 修复。
由于此发行版只包含 bug 修复，因此它可能已成为 EF 4.1.2 修补程序版本，但我们选择迁移到4.2，以允许我们离开在4.1 版本中使用的基于日期的修补程序版本号，并为 s 使用[语义 Versionsing](https://semver.org)标准emantic 版本控制。

下面是专为 EF 4.2 版本组合在一起的内容列表，为 EF 4.1 提供的内容也适用于 EF 4.2：

-   [EF 4.2 发布后](https://blogs.msdn.com/b/adonet/archive/2011/11/01/ef-4-2-released.aspx)
-   [Code First 演练](https://blogs.msdn.com/b/adonet/archive/2011/09/28/ef-4-2-code-first-walkthrough.aspx)
-   [Database First 演练 & 模型](https://blogs.msdn.com/b/adonet/archive/2011/09/28/ef-4-2-model-amp-database-first-walkthrough.aspx)

## <a name="ef-411"></a>EF 4.1。1
EF 4.1.10715 运行时已发布到2011年7月的 NuGet。
除了 bug 修复外，此修补程序版本还引入了一些组件，使设计时工具可以更轻松地处理 Code First 模型。
这些组件由 Code First 迁移（包括在 EF 4.3 中）和 EF Power Tools 使用。

你会注意到，包的异常版本号4.1.10715。
我们在决定采用[语义版本控制](https://semver.org)之前，使用基于日期的修补程序版本。
将此版本视为 EF 4.1 修补程序1（或 EF 4.1.1）。

下面是我们将4.1.1 版本组合在一起的内容列表：

-   [EF 4.1.1 Release Post](https://blogs.msdn.com/b/adonet/archive/2011/07/25/ef-4-1-update-1-released.aspx)

## <a name="ef-41"></a>EF 4。1
EF 4.1.10331 运行时是第一次在 NuGet 上发布，2011年4月。
此版本包括简化的 DbContext API 和 Code First 的工作流。

你会注意到奇怪的版本号，4.1.10331，其实际为4.1。 此外，还会有一个4.1.10311 版本，该版本应为4.1.0 （"rc" 代表 "候选发布"）。
我们在决定采用[语义版本控制](https://semver.org)之前，使用基于日期的修补程序版本。

下面是我们为4.1 版本组合在一起的内容列表。 很多情况仍适用于实体框架的更高版本：

-   [EF 4.1 发布后](https://blogs.msdn.com/b/adonet/archive/2011/04/11/ef-4-1-released.aspx)
-   [Code First 演练](https://blogs.msdn.com/b/adonet/archive/2011/03/15/ef-4-1-code-first-walkthrough.aspx)
-   [Database First 演练 & 模型](https://blogs.msdn.com/b/adonet/archive/2011/03/15/ef-4-1-model-amp-database-first-walkthrough.aspx)
-   [SQL Azure 联合和实体框架](https://blogs.msdn.com/b/adonet/archive/2012/01/10/sql-azure-federations-and-the-entity-framework.aspx)

## <a name="ef-40"></a>EF 4。0
此版本包含在2010的4月 .NET Framework 4 和 Visual Studio 2010 中。
此版本中的重要新功能包括 POCO 支持、外键映射、延迟加载、可测试性改进、可自定义代码生成和 Model First 工作流。

尽管它是实体框架的第二个版本，但它被命名为 EF 4，以与其随附的 .NET Framework 版本保持一致。
在此版本发布后，我们开始在 NuGet 上提供实体框架，并采纳语义版本控制，因为我们不再与 .NET Framework 版本关联。

请注意，某些后续版本的 .NET Framework 附带了包含 EF 位的重大更新。
事实上，EF 5.0 的许多新功能已实现为这些位的改进。
但是，为了合理化 EF 的版本控制情景，我们将继续引用作为 EF 4.0 运行时的一部分 .NET Framework 的 EF 位，而所有更新版本都包含[EntityFramework NuGet 包](https://www.nuget.org/packages/EntityFramework/)。

## <a name="ef-35"></a>EF 3。5
实体框架的初始版本包含在3.5 年 8 2008 月发布的 .NET Service Pack 1 和 Visual Studio 2008 SP1 中。
此版本提供了使用 Database First 工作流的基本 O/RM 支持。
