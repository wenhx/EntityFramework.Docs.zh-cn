---
title: 提供程序影响的更改的日志-EF Core
description: 影响提供程序的 Entity Framework Core 更改的日志
author: ajcvickers
ms.date: 08/08/2018
uid: core/providers/provider-log
ms.openlocfilehash: e701bac32c39f13be039d093931510a99070abbf
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065376"
---
# <a name="provider-impacting-changes"></a>提供程序影响的更改

此页包含的链接指向在 EF Core 存储库上发出的请求，这些请求可能需要其他数据库提供程序的作者做出反应。 目的是在将提供程序更新为新版本时，为现有的第三方数据库提供程序的作者提供一个起点。

我们正在从2.1 到2.2 的更改启动此日志。 在2.1 之前，我们 [`providers-beware`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware) [`providers-fyi`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi) 在问题和拉取请求上使用了和标签。

## <a name="22-----3x"></a>2.2---> 3。x

请注意，许多 [应用程序级别的重大更改](xref:core/what-is-new/ef-core-3.x/breaking-changes) 还会影响提供程序。

* <https://github.com/aspnet/EntityFrameworkCore/pull/14022>
  * 删除了过时的 Api 和折叠的可选参数重载
  * 已删除 DatabaseColumn. GetUnderlyingStoreType ( # A1
* <https://github.com/aspnet/EntityFrameworkCore/pull/14589>
  * 删除过时的 Api
* <https://github.com/aspnet/EntityFrameworkCore/pull/15044>
  * 由于在基实现中修复几个 bug 所需的行为更改，CharTypeMapping 的子类可能已中断。
* <https://github.com/aspnet/EntityFrameworkCore/pull/15090>
  * 添加了 IDatabaseModelFactory 的基类，并将其更新为使用参数对象来缓解将来的中断。
* <https://github.com/aspnet/EntityFrameworkCore/pull/15123>
  * 在 MigrationsSqlGenerator 中使用的参数对象可缓解未来的中断。
* <https://github.com/aspnet/EntityFrameworkCore/pull/14972>
  * 日志级别的显式配置需要对提供程序可能使用的 Api 进行一些更改。 具体而言，如果提供程序直接使用日志记录基础结构，此更改可能会破坏使用。 此外，使用基础结构 (的提供程序将需要从或派生公开) `LoggingDefinitions` `RelationalLoggingDefinitions` 。 有关示例，请参阅 SQL Server 和内存中提供程序。
* <https://github.com/aspnet/EntityFrameworkCore/pull/15091>
  * 核心、关系和抽象资源字符串现在是公共的。
  * `CoreLoggerExtensions` 和 `RelationalLoggerExtensions` 现在是公共的。 当记录在核心或关系级别定义的事件时，提供程序应使用这些 Api。 不要直接访问日志记录资源;这些都是内部的。
  * `IRawSqlCommandBuilder` 已从单一服务更改为范围服务
  * `IMigrationsSqlGenerator` 已从单一服务更改为范围服务
* <https://github.com/aspnet/EntityFrameworkCore/pull/14706>
  * 构建关系命令的基础结构已成为公共的，以便提供程序可以安全地使用它，并对其进行略微重构。
* <https://github.com/aspnet/EntityFrameworkCore/pull/14733>
  * `ILazyLoader` 已从作用域服务更改为暂时性服务
* <https://github.com/aspnet/EntityFrameworkCore/pull/14610>
  * `IUpdateSqlGenerator` 已从作用域服务更改为单一服务
  * 此外，已被 `ISingletonUpdateSqlGenerator` 删除
* <https://github.com/aspnet/EntityFrameworkCore/pull/15067>
  * 提供商现在使用的大量内部代码已公开
  * 它不应再 necssary 引用 `IndentedStringBuilder` ，因为它已从公开它的位置被分解
  * 的用法 `NonCapturingLazyInitializer` 应 `LazyInitializer` 从 BCL 替换为
* <https://github.com/aspnet/EntityFrameworkCore/pull/14608>
  * 应用程序重大更改文档完全涵盖了此更改。 对于提供程序，这可能会产生更大的影响，因为测试 EF core 常常会导致此问题，因此测试基础结构发生了变化以降低可能性。
* <https://github.com/aspnet/EntityFrameworkCore/issues/13961>
  * `EntityMaterializerSource` 已简化
* <https://github.com/aspnet/EntityFrameworkCore/pull/14895>
  * StartsWith 翻译以提供商可能需要/需要反应的方式进行了更改
* <https://github.com/aspnet/EntityFrameworkCore/pull/15168>
  * 约定集服务已更改。 提供程序现在应继承自 "ProviderConventionSet" 或 "RelationalConventionSet"。
  * 自定义项可以通过 `IConventionSetCustomizer` 服务添加，但这种方法旨在供其他扩展使用，而不是由提供程序使用。
  * 应从解析运行时使用的约定 `IConventionSetBuilder` 。
* <https://github.com/aspnet/EntityFrameworkCore/pull/15288>
  * 数据种子已重构为公共 API，以避免需要使用内部类型。 这只会影响非关系提供程序，因为种子设定由所有关系提供程序的基本关系类处理。

## <a name="21-----22"></a>2.1---> 2。2

### <a name="test-only-changes"></a>仅限测试更改

* <https://github.com/aspnet/EntityFrameworkCore/pull/12057> -允许在测试中自定义 SQL 分隔符
  * 在 BuiltInDataTypesTestBase 中允许非严格浮点比较的测试更改
  * 允许将查询测试与不同的 SQL 分隔符重新使用的测试更改
* <https://github.com/aspnet/EntityFrameworkCore/pull/12072> -将 DbFunction 测试添加到关系规范测试
  * 这些测试可以对所有数据库提供程序运行
* <https://github.com/aspnet/EntityFrameworkCore/pull/12362> -异步测试清理
  * 删除 `Wait` 调用、不需要的异步，并重命名某些测试方法
* <https://github.com/aspnet/EntityFrameworkCore/pull/12666> -统一日志测试基础结构
  * 添加 `CreateListLoggerFactory` 并删除了某些以前的日志记录基础结构，这将需要使用这些测试的提供程序来做出响应
* <https://github.com/aspnet/EntityFrameworkCore/pull/12500> -以同步和异步方式运行多个查询测试
  * 测试名称和因式分解已更改，这将要求使用这些测试的提供程序做出反应
* <https://github.com/aspnet/EntityFrameworkCore/pull/12766> -重命名 ComplexNavigations 模型中的导航
  * 使用这些测试的提供程序可能需要响应
* <https://github.com/aspnet/EntityFrameworkCore/pull/12141> -将上下文返回到池，而不是在功能测试中释放
  * 此更改包括一些可能要求提供程序做出反应的测试重构

### <a name="test-and-product-code-changes"></a>测试和产品代码更改

* <https://github.com/aspnet/EntityFrameworkCore/pull/12109> -合并 RelationalTypeMapping 方法
  * 在派生类中，简化了2.1 到 RelationalTypeMapping 的更改。 我们认为这是不会中断提供程序的，但提供程序可以在其派生的类型映射类中利用这一更改。
* <https://github.com/aspnet/EntityFrameworkCore/pull/12069> -标记或命名查询
  * 添加用于标记 LINQ 查询的基础结构，并使这些标记在 SQL 中显示为注释。 这可能需要提供程序在 SQL 生成中做出反应。
* <https://github.com/aspnet/EntityFrameworkCore/pull/13115> -通过 NTS 支持空间数据
  * 允许在提供程序外部注册类型映射和成员转换器
    * 提供程序必须调用 base。FindMapping 在其 ITypeMappingSource 实现中 ( # A1，使其正常工作
  * 按照此模式将空间支持添加到在提供程序中保持一致的提供程序。
* <https://github.com/aspnet/EntityFrameworkCore/pull/13199> -添加用于服务提供程序创建的增强型调试
  * 允许 DbContextOptionsExtensions 实现一个新的接口，该接口可帮助用户了解如何重新生成内部服务提供程序
* <https://github.com/aspnet/EntityFrameworkCore/pull/13289> -添加用于运行状况检查的后 API
  * 此 PR 添加了 `CanConnect` 将用于 ASP.NET Core 运行状况检查来确定数据库是否可用的概念。 默认情况下，关系实现仅调用 `Exist` ，但如果有必要，提供程序可实现不同的。 非关系提供程序将需要实现新的 API，才能使用运行状况检查。
* <https://github.com/aspnet/EntityFrameworkCore/pull/13306> -Update base RelationalTypeMapping 不设置 DbParameter 大小
  * 默认情况下，停止设置大小，因为它会导致截断。 如果需要设置大小，则提供程序可能需要添加自己的逻辑。
* <https://github.com/aspnet/EntityFrameworkCore/pull/13372> -RevEng：始终为 decimal 列指定列类型
  * 始终在基架代码中为十进制列配置列类型，而不是按约定配置列类型。
  * 提供程序不应在其端上进行任何更改。
* <https://github.com/aspnet/EntityFrameworkCore/pull/13469> -添加用于生成 SQL CASE 表达式的 CaseExpression
* <https://github.com/aspnet/EntityFrameworkCore/pull/13648> -添加了在 SqlFunctionExpression 上指定类型映射的功能，以改善参数和结果的存储类型推理。
