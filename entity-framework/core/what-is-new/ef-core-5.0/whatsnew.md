---
title: EF Core 5.0 中的新增功能
author: ajcvickers
ms.date: 01/29/2020
uid: core/what-is-new/ef-core-5.0/whatsnew.md
ms.openlocfilehash: e858379cc46abbef999fd32a3685e1d522524889
ms.sourcegitcommit: 89567d08c9d8bf9c33bb55a62f17067094a4065a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2020
ms.locfileid: "77052016"
---
# <a name="whats-new-in-ef-core-50"></a>EF Core 5.0 中的新增功能

EF Core 5.0 目前正在开发中。
此页面将包含每个预览版中引入的令人关注的更改的简要介绍。
EF Core 5.0 的第一个预览版初步预计将在 2020 年的第一个季度发布。

此页面不会复制 [EF Core 5.0](plan.md) 的计划。
计划中介绍了 EF Core 5.0 的整体主题，其中包括我们在交付最终版本之前打算包含的所有内容。

发布时，我们会将此处的链接添加到官方文档。

## <a name="preview-1-not-yet-shipped"></a>预览版 1（尚未发布）

### <a name="simple-logging"></a>简单的日志记录

此特性会添加类似于 EF6 中 `Database.Log` 的功能。
也就是说，它提供了一种从 EF Core 获取日志的简单方法，而不需要配置任何类型的外部日志记录框架。

初步文档包含在 [2019 年 12 月 5 日发布的 EF 每周状态](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)中。

其他文档可通过问题 [#2085](https://github.com/aspnet/EntityFramework.Docs/issues/2085) 进行跟踪。

### <a name="simple-way-to-get-generated-sql"></a>获取生成的 SQL 的简单方法

EF Core 5.0 引入了 `ToQueryString` 扩展方法，该方法会返回执行 LINQ 查询时 EF Core 生成的 SQL。

初步文档包含在 [2020 年 1 月 9 日发布的 EF 每周状态](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246)中。

其他文档可通过问题 [#1331](https://github.com/aspnet/EntityFramework.Docs/issues/1331) 进行跟踪。

### <a name="enhanced-debug-views"></a>增强的调试视图

调试视图是调试问题时查看 EF Core 内部情况的一种简单方法。
在一段时间之前，我们就已经实现了模型的调试视图。
对于 EF Core 5.0，我们使模型视图更易于读取，并在状态管理器中为跟踪的实体添加了新的调试视图。

初步文档包含在 [2019 年 12 月 12 日发布的 EF 每周状态](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206)中。

其他文档可通过问题 [#2086](https://github.com/aspnet/EntityFramework.Docs/issues/2086) 进行跟踪。

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a>可在初始化的 DbContext 上更改连接或连接字符串

现在可以更轻松地创建 DbContext 实例，而无需任何连接或连接字符串。
而且，现在可以在上下文实例上更改连接或连接字符串。
这使得同一个上下文实例能够动态地连接到不同的数据库。

文档可通过问题 [#2075](https://github.com/aspnet/EntityFramework.Docs/issues/2075) 进行跟踪。

### <a name="change-tracking-proxies"></a>更改跟踪代理

EF Core 现在可以生成自动实现 [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) 和 [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1) 的运行时代理。
这些代理会将实体属性的值更改直接报告给 EF Core，从而无需扫描更改。
不过，代理有其自身的一组限制，因此并不适合所有人使用。

文档可通过问题 [#2076](https://github.com/aspnet/EntityFramework.Docs/issues/2076) 进行跟踪。

### <a name="improved-handling-of-database-null-semantics"></a>改进了对数据库 null 语义的处理

关系数据库通常将 NULL 视为未知值，因此它不等于任何其他 NULL 值。
另一方面，C# 将 null 视为与其他任何 null 相比均相等的定义值。
默认情况下，EF Core 会转换查询，使查询使用 C# null 语义。
EF Core 5.0 极大地提升了这些转换操作的效率。

文档可通过问题 [#1612](https://github.com/aspnet/EntityFramework.Docs/issues/1612) 进行跟踪。

### <a name="indexer-properties"></a>索引器属性

EF Core 5.0 支持 C# 索引器属性的映射。
这使得实体能够充当属性包，实体中的列被映射为包中的命名属性。

文档可通过问题 [#2018](https://github.com/aspnet/EntityFramework.Docs/issues/2018) 进行跟踪。

### <a name="generation-of-check-constraints-for-enum-mappings"></a>为枚举映射生成检查约束

EF Core 5.0 迁移现可为枚举属性映射生成检查约束。
例如：

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN('Useful', 'Useless', 'Unknown'))
```

文档可通过问题 [#2082](https://github.com/aspnet/EntityFramework.Docs/issues/2082) 进行跟踪。

### <a name="query-translations-for-more-datetime-constructs"></a>查询转换以获取更多 DateTime 构造

现在，包含新 DataTime 构造的查询会被转换。
此外，SQL Server 函数 DateDiffWeek 现已映射。

文档可通过问题 [#2079](https://github.com/aspnet/EntityFramework.Docs/issues/2079) 进行跟踪。

### <a name="query-translations-for-more-byte-array-constructs"></a>查询转换以获取更多字节数组构造

现在，使用 Contains、Length、SequenceEqual 等对 byte[] 属性进行的查询会转换成 SQL。

初步文档包含在 [2019 年 12 月 5 日发布的 EF 每周状态](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)中。

更多文档可通过问题 [#2079](https://github.com/aspnet/EntityFramework.Docs/issues/2079) 进行跟踪。

### <a name="query-translation-for-reverse"></a>反向查询转换

现在，使用 `Reverse` 的查询会被转换。
例如：

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

文档可通过问题 [#2079](https://github.com/aspnet/EntityFramework.Docs/issues/2079) 进行跟踪。

### <a name="query-translation-for-bitwise-operators"></a>按位运算符的查询转换

现在，使用按位运算符的查询会在更多的情况下被转换，例如：

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

文档可通过问题 [#2079](https://github.com/aspnet/EntityFramework.Docs/issues/2079) 进行跟踪。

### <a name="query-translation-for-strings-on-cosmos"></a>Cosmos 上的字符串的查询转换

现在，在使用 Azure Cosmos DB 提供程序的情况下，使用字符串方法 Contains、StartsWith 和 EndsWith 的查询将被转换。

文档可通过问题 [#2079](https://github.com/aspnet/EntityFramework.Docs/issues/2079) 进行跟踪。
