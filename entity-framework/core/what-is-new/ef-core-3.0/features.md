---
title: EF Core 3.0 中的新增功能 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: 2EBE2CCC-E52D-483F-834C-8877F5EB0C0C
uid: core/what-is-new/ef-core-3.0/features
ms.openlocfilehash: a71aa01e81d9830d7b9e6cb01c200851100a15df
ms.sourcegitcommit: 87e72899d17602f7526d6ccd22f3c8ee844145df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69628427"
---
# <a name="new-features-included-in-ef-core-30-currently-in-preview"></a>EF Core 3.0 中包含的新功能（目前处于预览状态）

> [!IMPORTANT]
> 请注意，将来版本的功能集和计划会发生更改，尽管我们会尽力使此页面保持最新状态，但它可能不会始终反映我们的最新计划。

以下列表包括为 EF Core 3.0 计划的主要新功能。
大多数这些功能都不包含在当前预览中，但随着我们在 RTM 方面取得进展，这些功能将可用。

原因是，在发布之初，我们专注于实现计划中的[中断性变更](xref:core/what-is-new/ef-core-3.0/breaking-changes)。
其中许多中断性变更都是对 EF Core 的改进。
还需要进行许多其他变更来实现进一步改进。 

有关正在进行的 bug 修复和增强功能的完整列表，可查看[问题跟踪程序中的此查询](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3A3.0.0+sort%3Areactions-%2B1-desc)。

## <a name="linq-improvements"></a>LINQ 的改进 

[跟踪问题 #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)

此功能的相关工作已经开始，但当前预览中不包含此功能。

LINQ 可便于编写数据库查询，而无需离开所选的语言，同时还能利用丰富的类型信息来获取 IntelliSense 和编译时类型检查。
不过，LINQ 也支持编写数量不限的复杂查询，而这对于 LINQ 提供程序来说，一直都是一项巨大挑战。
在之前的几个 EF Core 版本中，我们找出了能够转换为 SQL 的查询的部分，并允许查询的其余部分在客户端的内存中执行，从而部分解决了这一问题。
在某些情况下，此客户端执行是可取的，但在其他许多情况下，这可能会导致低效的查询，直到应用程序被部署到生产时才被发现。
在 EF Core 3.0 中，我们计划对我们的 LINQ 实现的工作原理以及测试它的方式进行重大更改。
目标是使其更为可靠（例如，避免修补程序版本中的查询中断）、使其能够将更多表达式正确地转换为 SQL、使其在更多场景中生成更有效的查询以及防止无法检测到效率低下的查询。

## <a name="cosmos-db-support"></a>Cosmos DB 支持 

[跟踪问题 #8443](https://github.com/aspnet/EntityFrameworkCore/issues/8443)

当前预览中包含此功能，但尚不完善。 

我们正在致力于开发适用于 EF Core 的 Cosmos DB 提供程序，以便开发人员能够熟悉 EF 编程模型，从而轻松地将 Azure Cosmos DB 定目标为应用程序数据库。
目标是利用 Cosmos DB 的一些优势，如全局分发、“始终开启”可用性、弹性可伸缩性和低延迟，甚至包括 .NET 开发人员可以更轻松地访问它。
此提供程序将针对 Cosmos DB 中的 SQL API 启用大部分 EF Core 功能，如自动更改跟踪、LINQ 和值转换。
我们在 EF Core 2.2 之前开始这一开发，并且[我们已发布了一些提供程序的预览版](https://blogs.msdn.microsoft.com/dotnet/2018/10/17/announcing-entity-framework-core-2-2-preview-3/)。
新的计划是与 EF Core 3.0 一起继续开发提供程序。 

## <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a>与主体共享表的依赖实体现为可选项

[跟踪问题 #9005](https://github.com/aspnet/EntityFrameworkCore/issues/9005)

将在 EF Core 3.0 预览版 4 中引入此功能。

考虑下列模型：
```C#
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public OrderDetails Details { get; set; }
}

[Owned]
public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}
```

自 EF Core 3.0 起，如果 `OrderDetails` 由 `Order` 拥有且显式映射到同一张表中，则它将可能添加 `Order` 而不添加 `OrderDetails`，并且除主键外的所有 `OrderDetails` 属性都将映射到不为 null 的列中。

查询时，如果其任意所需属性均没有值，或者它在主键之外没有任何必需属性且所有属性均为 `null`，则 EF Core 会将 `OrderDetails` 设置为 `null`。

## <a name="c-80-support"></a>C#8.0 支持

[跟踪问题 #12047](https://github.com/aspnet/EntityFrameworkCore/issues/12047)
[跟踪问题 #10347](https://github.com/aspnet/EntityFrameworkCore/issues/10347)

此功能的相关工作已经开始，但当前预览中不包含此功能。

我们希望客户利用 [C# 8.0 推出的新功能](https://blogs.msdn.microsoft.com/dotnet/2018/11/12/building-c-8-0/)，如在使用 EF Core 的同时使用异步流（包括 `await foreach`）和可以为 null 的引用类型。

## <a name="reverse-engineering-of-database-views"></a>数据库视图的反向工程

[跟踪问题 #1679](https://github.com/aspnet/EntityFrameworkCore/issues/1679)

此功能包含在当前预览中。

在 EF Core 2.1 中引入并在 EF Core 3.0 中视为没有键的实体类型的[查询类型](xref:core/modeling/query-types)代表可以从数据库读取但无法更新的数据。
在大多数情况下，这一特性使它们非常适合数据库视图，因此我们计划在执行数据库视图的反向工程时自动创建没有键的实体类型。

## <a name="property-bag-entities"></a>属性包实体

[跟踪问题 #13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610) 和 [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914)

此功能的相关工作已经开始，但当前预览中不包含此功能。 

此功能可以启用在索引的属性（而非常规属性）中存储数据的实体，并能够使用同一 .NET 类的实例（可能像 `Dictionary<string, object>` 一样简单）来表示同一 EF Core 模型中的不同的实体类型。
此功能是支持多对多关系而无需联接实体的基石（[问题 #1368](https://github.com/aspnet/EntityFrameworkCore/issues/1368)），是针对 EF Core 提出改进要求最多的一项功能。

## <a name="ef-63-on-net-core"></a>.NET Core 上的 EF 6.3

[跟踪问题 EF6#271](https://github.com/aspnet/EntityFramework6/issues/271)

此功能的相关工作已经开始，但当前预览中不包含此功能。 

我们知道许多现有应用程序使用以前版本的 EF，而仅为了利用 .NET Core 将其移植到 EF Core 有时需要大量工作。
为此，我们将对下一版本的 EF 6 进行调整，以在 .NET Core 3.0 上运行。
我们这样做是为了在尽可能减少更改的情况下推动现有应用程序的移植。
将存在一些限制。 例如:
- 除了 .NET Core 上包含的 SQL Server 支持之外，它还需要新的提供程序才能与其他数据库一起使用
- 将不启用 SQL Server 的空间支持

另请注意，此时没有为 EF 6 计划新功能。
