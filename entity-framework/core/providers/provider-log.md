---
title: 提供程序影响的更改-EF Core 的日志
author: ajcvickers
ms.author: avickers
ms.date: 08/08/2018
ms.assetid: 7CEF496E-A5B0-4F5F-B68E-529609B23EF9
ms.technology: entity-framework-core
uid: core/providers/provider-log
ms.openlocfilehash: 61a58bd6119763d90731fac62343b983af510cb6
ms.sourcegitcommit: 87fcaba46535aa351db4bdb1231bd14b40e459b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2019
ms.locfileid: "59929870"
---
# <a name="provider-impacting-changes"></a>提供程序影响的更改

此页包含用于拉取请求可能需要的其他数据库提供程序以响应作者在 EF Core 存储库进行链接。 目的是要更新到新版本的提供商时，适用于现有第三方数据库提供程序的作者提供一个起始点。

我们此日志着手从 2.1 到 2.2 的更改。 在 2.1 之前，我们使用[ `providers-beware` ](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware)并[ `providers-fyi` ](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi)上我们的问题和拉取请求标签。

## <a name="22-----30"></a>2.2 ---> 3.0

请注意其中的很多[应用程序级别的重大更改](../what-is-new/ef-core-3.0/breaking-changes.md)还将影响提供程序。

* https://github.com/aspnet/EntityFrameworkCore/pull/14022
  * 删除过时的 Api 和折叠的可选参数重载
  * Removed DatabaseColumn.GetUnderlyingStoreType()
* https://github.com/aspnet/EntityFrameworkCore/pull/14589
  * 删除过时的 Api
* https://github.com/aspnet/EntityFrameworkCore/pull/15044
  * CharTypeMapping 子类可能已损坏修复在基实现中的几个 bug 所需的行为更改所导致。
* https://github.com/aspnet/EntityFrameworkCore/pull/15090
  * 为 IDatabaseModelFactory 添加基类和其进行更新以使用参数对象来缓解未来的分页符。
* https://github.com/aspnet/EntityFrameworkCore/pull/15123
  * 参数对象中用于 MigrationsSqlGenerator 缓解未来的分页符。
* https://github.com/aspnet/EntityFrameworkCore/pull/14972
  * 日志级别的显式配置所需的提供程序可能正在使用 api，一些更改。 具体而言，如果提供程序直接使用日志记录基础结构，此更改可能会破坏使用。 此外，使用的基础结构，它是公共） 的提供程序以后将需要派生自`LoggingDefinitions`或`RelationalLoggingDefinitions`。 请参阅 SQL Server 和内存中的提供程序示例。
* https://github.com/aspnet/EntityFrameworkCore/pull/15091
  * 核心、 关系型和抽象资源字符串现在是公共的。
  * `CoreLoggerExtensions` 和`RelationalLoggerExtensions`现在是公共的。 记录在核心或关系级别上定义的事件时，提供程序应使用这些 Api。 不直接调用访问日志记录资源它们仍内部。
  * `IRawSqlCommandBuilder` 已从单一实例服务更改为作用域服务
  * `IMigrationsSqlGenerator` 已从单一实例服务更改为作用域服务
* https://github.com/aspnet/EntityFrameworkCore/pull/14706
  * 用于构建的关系命令基础结构进行了公共使它能够安全地使用的提供程序并稍有重构。
* https://github.com/aspnet/EntityFrameworkCore/pull/14733
  * `ILazyLoader` 已从作用域内的服务更改为暂时性服务
* https://github.com/aspnet/EntityFrameworkCore/pull/14610
  * `IUpdateSqlGenerator` 已从作用域内的服务更改为单一实例服务
  * 此外，`ISingletonUpdateSqlGenerator`已删除
* https://github.com/aspnet/EntityFrameworkCore/pull/15067
  * 大量的提供程序使用的内部代码现在已变得公共
  * 它不应该再受必要引用`IndentedStringBuilder`由于它具有已无需编写显示它的位置
  * 用法`NonCapturingLazyInitializer`应替换为`LazyInitializer`BCL 从
* https://github.com/aspnet/EntityFrameworkCore/pull/14608
  * 在应用程序中的重大更改文档并未完全包括此更改。 对于提供程序，这可能是更大的影响可能通常导致测试 EF core 以便测试基础结构已更改，以使其成为更少可能会遇到此问题，因为。
* https://github.com/aspnet/EntityFrameworkCore/issues/13961
  * `EntityMaterializerSource` 已经进行了简化
* https://github.com/aspnet/EntityFrameworkCore/pull/14895
  * StartsWith 翻译已更改的方式提供程序可能希望/需要作出反应
* https://github.com/aspnet/EntityFrameworkCore/pull/15168
  * 约定集服务已更改。 提供程序现在应从"ProviderConventionSet"或"RelationalConventionSet"继承。
  * 可以通过添加自定义项`IConventionSetCustomizer`服务，但这旨在由其他扩展，不提供程序。
  * 在运行时使用的约定应从解析`IConventionSetBuilder`。
* https://github.com/aspnet/EntityFrameworkCore/pull/15288 数据种子设定已被重构到一个公共 API，以避免需要使用内部类型。 这应只影响非关系提供程序，因为种子设定由关系的所有提供程序的关系的基类。

## <a name="21-----22"></a>2.1 ---> 2.2

### <a name="test-only-changes"></a>仅限测试的更改

* [https://github.com/aspnet/EntityFrameworkCore/pull/12057](https://github.com/aspnet/EntityFrameworkCore/pull/12057) -在测试中允许可自定义 SQL 分隔符
  * 测试 BuiltInDataTypesTestBase 中允许非严格的浮动点比较的更改
  * 测试更改，可查询的测试以重新用于其他 SQL 分隔符
* [https://github.com/aspnet/EntityFrameworkCore/pull/12072](https://github.com/aspnet/EntityFrameworkCore/pull/12072) -将 DbFunction 测试添加到关系规范测试
  * 如此一来，可以针对所有数据库提供程序运行这些测试
* [https://github.com/aspnet/EntityFrameworkCore/pull/12362](https://github.com/aspnet/EntityFrameworkCore/pull/12362) -异步测试清理
  * 删除`Wait`调用，不需要的异步，和重命名某些测试方法
* [https://github.com/aspnet/EntityFrameworkCore/pull/12666](https://github.com/aspnet/EntityFrameworkCore/pull/12666) -统一日志记录测试基础结构
  * 添加`CreateListLoggerFactory`和删除一些以前日志记录基础结构，这将需要使用这些测试能够做出反应的提供程序
* [https://github.com/aspnet/EntityFrameworkCore/pull/12500](https://github.com/aspnet/EntityFrameworkCore/pull/12500) -以异步方式和同步方式运行更多查询测试
  * 测试名称和组成要素已更改，这将需要使用这些测试能够做出反应的提供程序
* [https://github.com/aspnet/EntityFrameworkCore/pull/12766](https://github.com/aspnet/EntityFrameworkCore/pull/12766) -重命名 ComplexNavigations 模型中的导航
  * 提供程序使用这些测试可能需要做出响应
* [https://github.com/aspnet/EntityFrameworkCore/pull/12141](https://github.com/aspnet/EntityFrameworkCore/pull/12141) -返回到池而不是功能测试中释放上下文
  * 此更改包括这可能需要提供程序以响应某些测试重构

### <a name="test-and-product-code-changes"></a>测试和产品代码更改

* [https://github.com/aspnet/EntityFrameworkCore/pull/12109](https://github.com/aspnet/EntityFrameworkCore/pull/12109) -合并 RelationalTypeMapping.Clone 方法
  * 允许在派生类中简化 RelationalTypeMapping 到 2.1 中的更改。 我们不相信这中断到提供程序，但提供程序可以充分利用此更改及其派生类型中将类映射。
* [https://github.com/aspnet/EntityFrameworkCore/pull/12069](https://github.com/aspnet/EntityFrameworkCore/pull/12069) -标记或命名查询
  * 添加用于标记 LINQ 查询并显示为 SQL 中的注释这些标记的基础结构。 这可能需要提供程序中的 SQL 生成的做出响应。
* [https://github.com/aspnet/EntityFrameworkCore/pull/13115](https://github.com/aspnet/EntityFrameworkCore/pull/13115) -支持通过 NTS 空间数据
  * 允许类型映射和成员翻译人员外部提供程序注册
    * 提供程序必须调用基。在其工作其 ITypeMappingSource 实现中 FindMapping()
  * 遵循此模式将空间支持添加到您在提供程序都是一致的提供商。
* [https://github.com/aspnet/EntityFrameworkCore/pull/13199](https://github.com/aspnet/EntityFrameworkCore/pull/13199) -添加用于创建服务提供程序增强调试
  * 允许 DbContextOptionsExtensions 实现新的接口，可帮助用户了解为什么将内部服务提供商正在重新生成
* [https://github.com/aspnet/EntityFrameworkCore/pull/13289](https://github.com/aspnet/EntityFrameworkCore/pull/13289) -添加连接 API 使用的运行状况检查
  * 此拉取请求将添加这一概念`CanConnect`将由 ASP.NET Core 运行状况检查来确定数据库是否可用。 默认情况下，关系的实现只需调用`Exist`，但如有必要提供程序可以实现一些不同。 非关系提供程序将需要使运行状况检查能够实现新的 API。
* [https://github.com/aspnet/EntityFrameworkCore/pull/13306](https://github.com/aspnet/EntityFrameworkCore/pull/13306) -更新基 RelationalTypeMapping 不设置 DbParameter 大小
  * 停止默认情况下设置大小，因为它可能会导致截断。 提供程序可能需要添加其自己的逻辑，如果需要设置大小。
* (https://github.com/aspnet/EntityFrameworkCore/pull/13372) -RevEng:始终指定列的 decimal 列类型
  * 始终在基架的代码，而不是通过约定配置中配置 decimal 列的列的类型。
  * 提供程序不应要求其端上的任何更改。
* [https://github.com/aspnet/EntityFrameworkCore/pull/13469](https://github.com/aspnet/EntityFrameworkCore/pull/13469) -用于生成 SQL 用例表达式添加 CaseExpression
* [https://github.com/aspnet/EntityFrameworkCore/pull/13648](https://github.com/aspnet/EntityFrameworkCore/pull/13648) -添加了 SqlFunctionExpression 来提高存储类型推断的参数和结果上指定类型映射的功能。
