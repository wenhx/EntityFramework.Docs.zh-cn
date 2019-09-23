---
title: EF Core 3.0 中的新增功能 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: 2EBE2CCC-E52D-483F-834C-8877F5EB0C0C
uid: core/what-is-new/ef-core-3.0/features
ms.openlocfilehash: 528733d6eec33de2c9538541a6ed5be704b9d433
ms.sourcegitcommit: d01fc19aa42ca34c3bebccbc96ee26d06fcecaa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2019
ms.locfileid: "71005560"
---
# <a name="new-features-included-in-ef-core-30"></a>EF Core 3.0 中包含的新功能

以下列表包括为 EF Core 3.0 计划的主要新功能。

EF Core 3.0 是一个主要版本，还包含许多[中断性变更](xref:core/what-is-new/ef-core-3.0/breaking-changes)，即可能对现有应用程序产生负面影响的 API 改进。  

## <a name="linq-improvements"></a>LINQ 的改进 

通过 LINQ，可继续使用所选语言编写数据库查询，利用丰富的类型信息来提供 IntelliSense 和编译时类型检查。
而 LINQ 还允许编写包含任意表达式（方法调用或操作）的不限数量的复杂查询。
处理所有这些组合一直是 LINQ 提供程序面临的重大挑战。
在 EF Core 3.0 中，我们重写了 LINQ 实现，以便将更多的表达式转换为 SQL，在更多情况下生成高效查询，有效发现低效查询，并更容易地在不中断现有应用程序和数据提供程序的情况下逐步引入新的查询功能和性能改进。

### <a name="client-evaluation"></a>客户端求值

EF Core 3.0 的主要设计更改与它如何处理无法转换为 SQL 或参数的 LINQ 表达式有关：

在前几个版本中，EF Core 只仅明确查询的哪些部分可以转换为 SQL，并在客户端上执行查询的其余部分。
在某些情况下，这种客户端执行是可取的，但在其他许多情况下，这可能会导致低效的查询。
例如，如果 EF Core 2.2 无法转换 `Where()` 调用中的谓词，则会执行一个不带筛选器的 SQL 语句，从数据库中读取所有行，然后在内存中对其进行筛选。
如果数据库包含少量行，那么这可能是可以接受的，但如果数据库包含大量行，则可能导致严重的性能问题甚至应用程序故障。
在 EF Core 3.0 中，我们限制客户端评估仅发生在顶级投影（最后一次调用 `Select()`）上。
当 EF Core 3.0 检测到无法在查询中的任何其他位置转换的表达式时，它会引发运行时异常。

## <a name="cosmos-db-support"></a>Cosmos DB 支持 

通过 EF Core 的 Cosmos DB 提供程序，熟悉 EF 编程模型的开发人员可以轻松地将 Azure Cosmos DB 定为应用程序数据库目标。
目标是利用 Cosmos DB 的一些优势，如全局分发、“始终开启”可用性、弹性可伸缩性和低延迟，甚至包括 .NET 开发人员可以更轻松地访问它。
此提供程序将针对 Cosmos DB 中的 SQL API 启用大部分 EF Core 功能，如自动更改跟踪、LINQ 和值转换。

## <a name="c-80-support"></a>C#8.0 支持

EF Core 3.0 利用了 C#8.0 中的一些新功能：

### <a name="asynchronous-streams"></a>异步流

异步查询结果现在使用新的标准 `IAsyncEnumerable<T>` 接口公开，并且可以通过 `await foreach` 使用。

``` csharp
var orders = 
  from o in context.Orders
  where o.Status == OrderStatus.Pending
  select o;

await foreach(var o in orders)
{
  Proccess(o);
} 
```

### <a name="nullable-reference-types"></a>可为空引用类型 

当在代码中启用这个新功能时，EF Core 可以推断引用类型的属性（字符串等基本类型或导航属性）的为 Null 性，从而决定数据库中列和关系的为 Null 性。

## <a name="interception"></a>Interception

EF Core 3.0 中新的拦截 API 允许以编程方式观察和修改属于正常 EF Core 操作的低级数据库操作（例如打开连接、初始化事务和执行命令等）的结果。 

## <a name="reverse-engineering-of-database-views"></a>数据库视图的反向工程

没有键的实体类型（以前称为[查询类型](xref:core/modeling/query-types)）表示可以从数据库读取但不能更新的数据。
在大多数情况下，这一特性使它们非常适合映射数据库视图，因此我们在执行数据库视图的反向工程时自动创建了没有键的实体类型。

## <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a>与主体共享表的依赖实体现为可选项

自 EF Core 3.0 起，如果 `OrderDetails` 由 `Order` 拥有且显式映射到同一张表中，则它将可能添加 `Order` 而不添加 `OrderDetails`，并且除主键外的所有 `OrderDetails` 属性都将映射到不为 null 的列中。

查询时，如果其任意所需属性均没有值，或者它在主键之外没有任何必需属性且所有属性均为 `null`，则 EF Core 会将 `OrderDetails` 设置为 `null`。

``` csharp
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

## <a name="ef-63-on-net-core"></a>.NET Core 上的 EF 6.3

我们知道许多现有应用程序使用以前版本的 EF，而仅为了利用 .NET Core 将其移植到 EF Core 有时需要大量工作。
为此，我们允许了最新版本的 EF 6 在 .NET Core 3.0 上运行。
有一些限制，例如：
- 新的提供程序需要在 .NET Core 上运行
- 将不启用 SQL Server 的空间支持

## <a name="postponed-features"></a>推迟的功能

最初计划为 EF Core 3.0 提供的一些功能已推迟到将来的版本： 

- 在迁移部分忽略模型的功能，跟踪编号为 [#2725](https://github.com/aspnet/EntityFrameworkCore/issues/2725)。
- 属性包实体，由两个单独的问题跟踪：关于共享类型实体的 [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914) 和关于索引属性映射支持的 [#13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610)。
