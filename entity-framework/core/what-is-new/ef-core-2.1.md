---
title: EF Core 2.1 中的新增功能 - EF Core
author: divega
ms.date: 02/20/2018
ms.assetid: 585F90A3-4D5A-4DD1-92D8-5243B14E0FEC
uid: core/what-is-new/ef-core-2.1
ms.openlocfilehash: ba3a26bcd76cd0b9615b13f32456e7280afe533a
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2020
ms.locfileid: "78413563"
---
# <a name="new-features-in-ef-core-21"></a><span data-ttu-id="25b8f-102">EF Core 2.1 中的新增功能</span><span class="sxs-lookup"><span data-stu-id="25b8f-102">New features in EF Core 2.1</span></span>

<span data-ttu-id="25b8f-103">除了大量缺陷修复以及小规模功能增强和性能增强之外，EF Core 2.1 还新增了一些有吸引力的功能：</span><span class="sxs-lookup"><span data-stu-id="25b8f-103">Besides numerous bug fixes and small functional and performance enhancements, EF Core 2.1 includes some compelling new features:</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="25b8f-104">延迟加载</span><span class="sxs-lookup"><span data-stu-id="25b8f-104">Lazy loading</span></span>

<span data-ttu-id="25b8f-105">EF Core 现包含创作可按需加载导航属性的实体类所必需的构建基块。</span><span class="sxs-lookup"><span data-stu-id="25b8f-105">EF Core now contains the necessary building blocks for anyone to author entity classes that can load their navigation properties on demand.</span></span> <span data-ttu-id="25b8f-106">我们还创建了一个新包 - Microsoft.EntityFrameworkCore.Proxies，它可利用这些构建基块基于最小修改实体类（例如具有虚拟导航属性的类）来生成延迟加载代理类。</span><span class="sxs-lookup"><span data-stu-id="25b8f-106">We have also created a new package, Microsoft.EntityFrameworkCore.Proxies, that leverages those building blocks to produce lazy loading proxy classes based on minimally modified entity classes (for example, classes with virtual navigation properties).</span></span>

<span data-ttu-id="25b8f-107">阅读[有关延迟加载的部分](xref:core/querying/related-data#lazy-loading)详细了解本主题。</span><span class="sxs-lookup"><span data-stu-id="25b8f-107">Read the [section on lazy loading](xref:core/querying/related-data#lazy-loading) for more information about this topic.</span></span>

## <a name="parameters-in-entity-constructors"></a><span data-ttu-id="25b8f-108">实体构造函数中的参数</span><span class="sxs-lookup"><span data-stu-id="25b8f-108">Parameters in entity constructors</span></span>

<span data-ttu-id="25b8f-109">作为延迟加载所必需的一个构建基块，我们启用了实体创建，实体可将参数纳入其构造函数中。</span><span class="sxs-lookup"><span data-stu-id="25b8f-109">As one of the required building blocks for lazy loading, we enabled the creation of entities that take parameters in their constructors.</span></span> <span data-ttu-id="25b8f-110">可使用参数来注入属性值、延迟加载委托和服务。</span><span class="sxs-lookup"><span data-stu-id="25b8f-110">You can use parameters to inject property values, lazy loading delegates, and services.</span></span>

<span data-ttu-id="25b8f-111">阅读[有关含参数的实体构造函数部分](xref:core/modeling/constructors)详细了解本主题。</span><span class="sxs-lookup"><span data-stu-id="25b8f-111">Read the [section on entity constructor with parameters](xref:core/modeling/constructors) for more information about this topic.</span></span>

## <a name="value-conversions"></a><span data-ttu-id="25b8f-112">值转换</span><span class="sxs-lookup"><span data-stu-id="25b8f-112">Value conversions</span></span>

<span data-ttu-id="25b8f-113">到目前为止，EF Core 只能映射底层数据库提供程序本机支持的属性类型。</span><span class="sxs-lookup"><span data-stu-id="25b8f-113">Until now, EF Core could only map properties of types natively supported by the underlying database provider.</span></span> <span data-ttu-id="25b8f-114">在列和属性之间来回复制值，无需进行任何转换。</span><span class="sxs-lookup"><span data-stu-id="25b8f-114">Values were copied back and forth between columns and properties without any transformation.</span></span> <span data-ttu-id="25b8f-115">自 EF Core 2.1 起，可通过值转换来转换从列获取的值，之后再将其应用到属性，反之亦然。</span><span class="sxs-lookup"><span data-stu-id="25b8f-115">Starting with EF Core 2.1, value conversions can be applied to transform the values obtained from columns before they are applied to properties, and vice versa.</span></span> <span data-ttu-id="25b8f-116">我们具有可以根据约定按需应用的大量转换，以及显式配置 API，后者允许在列和属性之间注册自定义转换。</span><span class="sxs-lookup"><span data-stu-id="25b8f-116">We have a number of conversions that can be applied by convention as necessary, as well as an explicit configuration API that allows registering custom conversions between columns and properties.</span></span> <span data-ttu-id="25b8f-117">此功能有如下一些用法：</span><span class="sxs-lookup"><span data-stu-id="25b8f-117">Some of the application of this feature are:</span></span>

- <span data-ttu-id="25b8f-118">将枚举存储为字符串</span><span class="sxs-lookup"><span data-stu-id="25b8f-118">Storing enums as strings</span></span>
- <span data-ttu-id="25b8f-119">使用 SQL Server 映射无符号整数</span><span class="sxs-lookup"><span data-stu-id="25b8f-119">Mapping unsigned integers with SQL Server</span></span>
- <span data-ttu-id="25b8f-120">自动加密和解密属性值</span><span class="sxs-lookup"><span data-stu-id="25b8f-120">Automatic encryption and decryption of property values</span></span>

<span data-ttu-id="25b8f-121">阅读[有关值转换的部分](xref:core/modeling/value-conversions)详细了解本主题。</span><span class="sxs-lookup"><span data-stu-id="25b8f-121">Read the [section on value conversions](xref:core/modeling/value-conversions) for more information about this topic.</span></span>  

## <a name="linq-groupby-translation"></a><span data-ttu-id="25b8f-122">LINQ GroupBy 转换</span><span class="sxs-lookup"><span data-stu-id="25b8f-122">LINQ GroupBy translation</span></span>

<span data-ttu-id="25b8f-123">在 2.1 版之前，EF Core 中的 GroupBy LINQ 运算符将始终在内存中进行计算。</span><span class="sxs-lookup"><span data-stu-id="25b8f-123">Before version 2.1, in EF Core the GroupBy LINQ operator would always be evaluated in memory.</span></span> <span data-ttu-id="25b8f-124">在大多数情况下，我们现在支持将其转换为 SQL GROUP BY 子句。</span><span class="sxs-lookup"><span data-stu-id="25b8f-124">We now support translating it to the SQL GROUP BY clause in most common cases.</span></span>

<span data-ttu-id="25b8f-125">此示例显示了一个用 GroupBy 来计算各种聚合函数的查询：</span><span class="sxs-lookup"><span data-stu-id="25b8f-125">This example shows a query with GroupBy used to compute various aggregate functions:</span></span>

``` csharp
var query = context.Orders
    .GroupBy(o => new { o.CustomerId, o.EmployeeId })
    .Select(g => new
        {
          g.Key.CustomerId,
          g.Key.EmployeeId,
          Sum = g.Sum(o => o.Amount),
          Min = g.Min(o => o.Amount),
          Max = g.Max(o => o.Amount),
          Avg = g.Average(o => o.Amount)
        });
```

<span data-ttu-id="25b8f-126">相应的 SQL 转化如下所示：</span><span class="sxs-lookup"><span data-stu-id="25b8f-126">The corresponding SQL translation looks like this:</span></span>

``` SQL
SELECT [o].[CustomerId], [o].[EmployeeId],
    SUM([o].[Amount]), MIN([o].[Amount]), MAX([o].[Amount]), AVG([o].[Amount])
FROM [Orders] AS [o]
GROUP BY [o].[CustomerId], [o].[EmployeeId];
```

## <a name="data-seeding"></a><span data-ttu-id="25b8f-127">数据种子设定</span><span class="sxs-lookup"><span data-stu-id="25b8f-127">Data Seeding</span></span>

<span data-ttu-id="25b8f-128">新版本可提供初始数据来填充数据库。</span><span class="sxs-lookup"><span data-stu-id="25b8f-128">With the new release it will be possible to provide initial data to populate a database.</span></span> <span data-ttu-id="25b8f-129">与 EF6 不同，种子设定数据作为模型配置的一部分与实体类型相关联。</span><span class="sxs-lookup"><span data-stu-id="25b8f-129">Unlike in EF6, seeding data is associated to an entity type as part of the model configuration.</span></span> <span data-ttu-id="25b8f-130">随后将数据库升级为新版本模型时，EF Core 迁移会自动计算需要应用的插入、更新或删除操作。</span><span class="sxs-lookup"><span data-stu-id="25b8f-130">Then EF Core migrations can automatically compute what insert, update or delete operations need to be applied when upgrading the database to a new version of the model.</span></span>

<span data-ttu-id="25b8f-131">如示例所示，可使用它在 `OnModelCreating` 中为 Post 配置种子数据：</span><span class="sxs-lookup"><span data-stu-id="25b8f-131">As an example, you can use this to configure seed data for a Post in `OnModelCreating`:</span></span>

``` csharp
modelBuilder.Entity<Post>().HasData(new Post{ Id = 1, Text = "Hello World!" });
```

<span data-ttu-id="25b8f-132">阅读[有关数据种子设定的部分](xref:core/modeling/data-seeding)详细了解本主题。</span><span class="sxs-lookup"><span data-stu-id="25b8f-132">Read the [section on data seeding](xref:core/modeling/data-seeding) for more information about this topic.</span></span>  

## <a name="query-types"></a><span data-ttu-id="25b8f-133">查询类型</span><span class="sxs-lookup"><span data-stu-id="25b8f-133">Query types</span></span>

<span data-ttu-id="25b8f-134">EF Core 模型现可包含查询类型。</span><span class="sxs-lookup"><span data-stu-id="25b8f-134">An EF Core model can now include query types.</span></span> <span data-ttu-id="25b8f-135">与实体类型不同，查询类型上未定义键，也不能插入、删除或更新查询类型（即它们为只读），但查询可直接返回查询类型。</span><span class="sxs-lookup"><span data-stu-id="25b8f-135">Unlike entity types, query types do not have keys defined on them and cannot be inserted, deleted or updated (that is, they are read-only), but they can be returned directly by queries.</span></span> <span data-ttu-id="25b8f-136">以下是查询类型的一些用法：</span><span class="sxs-lookup"><span data-stu-id="25b8f-136">Some of the usage scenarios for query types are:</span></span>

- <span data-ttu-id="25b8f-137">映射到没有主键的视图</span><span class="sxs-lookup"><span data-stu-id="25b8f-137">Mapping to views without primary keys</span></span>
- <span data-ttu-id="25b8f-138">映射到没有主键的表</span><span class="sxs-lookup"><span data-stu-id="25b8f-138">Mapping to tables without primary keys</span></span>
- <span data-ttu-id="25b8f-139">映射到模型中定义的查询</span><span class="sxs-lookup"><span data-stu-id="25b8f-139">Mapping to queries defined in the model</span></span>
- <span data-ttu-id="25b8f-140">用作 `FromSql()` 查询的返回类型</span><span class="sxs-lookup"><span data-stu-id="25b8f-140">Serving as the return type for `FromSql()` queries</span></span>

<span data-ttu-id="25b8f-141">阅读[有关查询类型的部分](xref:core/modeling/keyless-entity-types)详细了解本主题。</span><span class="sxs-lookup"><span data-stu-id="25b8f-141">Read the [section on query types](xref:core/modeling/keyless-entity-types) for more information about this topic.</span></span>

## <a name="include-for-derived-types"></a><span data-ttu-id="25b8f-142">针对派生类型的 Include</span><span class="sxs-lookup"><span data-stu-id="25b8f-142">Include for derived types</span></span>

<span data-ttu-id="25b8f-143">现可在编写 `Include` 方法的表达式时指定仅在派生类型上定义的导航属性。</span><span class="sxs-lookup"><span data-stu-id="25b8f-143">It will be now possible to specify navigation properties only defined on derived types when writing expressions for the `Include` method.</span></span> <span data-ttu-id="25b8f-144">对于 `Include` 的强类型版本，我们支持使用显式强制转换或 `as` 运算符。</span><span class="sxs-lookup"><span data-stu-id="25b8f-144">For the strongly typed version of `Include`, we support using either an explicit cast or the `as` operator.</span></span> <span data-ttu-id="25b8f-145">我们现在还支持在 `Include` 的字符串版本中引用在派生类型上定义的导航属性的名称：</span><span class="sxs-lookup"><span data-stu-id="25b8f-145">We also now support referencing the names of navigation property defined on derived types in the string version of `Include`:</span></span>

``` csharp
var option1 = context.People.Include(p => ((Student)p).School);
var option2 = context.People.Include(p => (p as Student).School);
var option3 = context.People.Include("School");
```

<span data-ttu-id="25b8f-146">阅读[有关派生类型的 Include 部分](xref:core/querying/related-data#include-on-derived-types)详细了解本主题。</span><span class="sxs-lookup"><span data-stu-id="25b8f-146">Read the [section on Include with derived types](xref:core/querying/related-data#include-on-derived-types) for more information about this topic.</span></span>

## <a name="systemtransactions-support"></a><span data-ttu-id="25b8f-147">System.Transactions 支持</span><span class="sxs-lookup"><span data-stu-id="25b8f-147">System.Transactions support</span></span>

<span data-ttu-id="25b8f-148">我们增加了对 System.Transactions 功能（如 TransactionScope）的应用。</span><span class="sxs-lookup"><span data-stu-id="25b8f-148">We have added the ability to work with System.Transactions features such as TransactionScope.</span></span> <span data-ttu-id="25b8f-149">使用支持该功能的数据库提供程序时，这将适用于 .NET Framework 和 .NET Core。</span><span class="sxs-lookup"><span data-stu-id="25b8f-149">This will work on both .NET Framework and .NET Core when using database providers that support it.</span></span>

<span data-ttu-id="25b8f-150">阅读[有关 System.Transactions 的部分](xref:core/saving/transactions#using-systemtransactions)详细了解本主题。</span><span class="sxs-lookup"><span data-stu-id="25b8f-150">Read the [section on System.Transactions](xref:core/saving/transactions#using-systemtransactions) for more information about this topic.</span></span>

## <a name="better-column-ordering-in-initial-migration"></a><span data-ttu-id="25b8f-151">初始迁移时生成更好的列顺序</span><span class="sxs-lookup"><span data-stu-id="25b8f-151">Better column ordering in initial migration</span></span>

<span data-ttu-id="25b8f-152">根据客户反馈，我们对迁移进行了更新，使得先以与类中声明的属性相同的顺序为表生成列。</span><span class="sxs-lookup"><span data-stu-id="25b8f-152">Based on customer feedback, we have updated migrations to initially generate columns for tables in the same order as properties are declared in classes.</span></span> <span data-ttu-id="25b8f-153">请注意，在创建初始表后，添加新成员时，EF Core 不能更改顺序。</span><span class="sxs-lookup"><span data-stu-id="25b8f-153">Note that EF Core cannot change order when new members are added after the initial table creation.</span></span>

## <a name="optimization-of-correlated-subqueries"></a><span data-ttu-id="25b8f-154">相关子查询优化</span><span class="sxs-lookup"><span data-stu-id="25b8f-154">Optimization of correlated subqueries</span></span>

<span data-ttu-id="25b8f-155">我们改进了查询转换，避免在许多常见情况下执行“N + 1”SQL 查询，一般情况下，在投影中使用导航属性后，来自根查询的数据会与来自相关子查询的数据相连接。</span><span class="sxs-lookup"><span data-stu-id="25b8f-155">We have improved our query translation to avoid executing "N + 1" SQL queries in many common scenarios in which the usage of a navigation property in the projection leads to joining data from the root query with data from a correlated subquery.</span></span> <span data-ttu-id="25b8f-156">进行优化需要缓冲子查询的结果，且我们要求修改查询，选择新行为。</span><span class="sxs-lookup"><span data-stu-id="25b8f-156">The optimization requires buffering the results from the subquery, and we require that you modify the query to opt-in the new behavior.</span></span>

<span data-ttu-id="25b8f-157">例如，以下查询通常会转换为：一个“客户”查询，加上 N（其中“N”是返回的客户数量）个单独的“订单”查询：</span><span class="sxs-lookup"><span data-stu-id="25b8f-157">As an example, the following query normally gets translated into one query for Customers, plus N (where "N" is the number of customers returned) separate queries for Orders:</span></span>

``` csharp
var query = context.Customers.Select(
    c => c.Orders.Where(o => o.Amount  > 100).Select(o => o.Amount));
```

<span data-ttu-id="25b8f-158">将 `ToList()` 放入正确的位置，指示缓冲适用于订单，即可启用优化：</span><span class="sxs-lookup"><span data-stu-id="25b8f-158">By including `ToList()` in the right place, you indicate that buffering is appropriate for the Orders, which enable the optimization:</span></span>

``` csharp
var query = context.Customers.Select(
    c => c.Orders.Where(o => o.Amount  > 100).Select(o => o.Amount).ToList());
```

<span data-ttu-id="25b8f-159">请注意，此查询只会被转换为两个 SQL 查询：一个“客户”查询，一个“订单”查询。</span><span class="sxs-lookup"><span data-stu-id="25b8f-159">Note that this query will be translated to only two SQL queries: One for Customers and the next one for Orders.</span></span>

## <a name="owned-attribute"></a><span data-ttu-id="25b8f-160">[Owned] 属性</span><span class="sxs-lookup"><span data-stu-id="25b8f-160">[Owned] attribute</span></span>

<span data-ttu-id="25b8f-161">现只需使用 `[Owned]` 注释类型，并确保所有者实体添加到了模型中，即可配置[固有实体类型](xref:core/modeling/owned-entities)：</span><span class="sxs-lookup"><span data-stu-id="25b8f-161">It is now possible to configure [owned entity types](xref:core/modeling/owned-entities) by simply annotating the type with `[Owned]` and then making sure the owner entity is added to the model:</span></span>

``` csharp
[Owned]
public class StreetAddress
{
    public string Street { get; set; }
    public string City { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public StreetAddress ShippingAddress { get; set; }
}
```

## <a name="command-line-tool-dotnet-ef-included-in-net-core-sdk"></a><span data-ttu-id="25b8f-162">.NET Core SDK 中包含的命令行工具 dotnet-ef</span><span class="sxs-lookup"><span data-stu-id="25b8f-162">Command-line tool dotnet-ef included in .NET Core SDK</span></span>

<span data-ttu-id="25b8f-163">dotnet-ef 命令现在是 .NET Core SDK 的一部分，因此无须在项目中使用 DotNetCliToolReference 即可使用各项迁移，或通过现有数据库搭建 DbContext 基架  。</span><span class="sxs-lookup"><span data-stu-id="25b8f-163">The _dotnet-ef_ commands are now part of the .NET Core SDK, therefore it will no longer be necessary to use DotNetCliToolReference in the project to be able to use migrations or to scaffold a DbContext from an existing database.</span></span>

<span data-ttu-id="25b8f-164">有关如何为不同版本的 .NET Core SDK 和 EF Core 启用命令行工具的详细信息，请参阅[安装工具](xref:core/miscellaneous/cli/dotnet#installing-the-tools)的相关部分。</span><span class="sxs-lookup"><span data-stu-id="25b8f-164">See the section on [installing the tools](xref:core/miscellaneous/cli/dotnet#installing-the-tools) for more details on how to enable command line tools for different versions of the .NET Core SDK and EF Core.</span></span>

## <a name="microsoftentityframeworkcoreabstractions-package"></a><span data-ttu-id="25b8f-165">Microsoft.EntityFrameworkCore.Abstractions 包</span><span class="sxs-lookup"><span data-stu-id="25b8f-165">Microsoft.EntityFrameworkCore.Abstractions package</span></span>

<span data-ttu-id="25b8f-166">可以在项目中使用新包内的一些属性和接口，从而启用 EF Core 功能，而无需依赖 EF Core 整体。</span><span class="sxs-lookup"><span data-stu-id="25b8f-166">The new package contains attributes and interfaces that you can use in your projects to light up EF Core features without taking a dependency on EF Core as a whole.</span></span> <span data-ttu-id="25b8f-167">例如，[Owned] 属性和 ILazyLoader 接口位于此处。</span><span class="sxs-lookup"><span data-stu-id="25b8f-167">For example, the [Owned] attribute and the ILazyLoader interface are located here.</span></span>

## <a name="state-change-events"></a><span data-ttu-id="25b8f-168">状态更改事件</span><span class="sxs-lookup"><span data-stu-id="25b8f-168">State change events</span></span>

<span data-ttu-id="25b8f-169">`ChangeTracker` 中新增的 `Tracked` 和 `StateChanged` 事件可用于编写逻辑，以响应进入 DbContext 或状态更改的实体。</span><span class="sxs-lookup"><span data-stu-id="25b8f-169">New `Tracked` And `StateChanged` events on `ChangeTracker` can be used to write logic that reacts to entities entering the DbContext or changing their state.</span></span>

## <a name="raw-sql-parameter-analyzer"></a><span data-ttu-id="25b8f-170">原始 SQL 参数分析器</span><span class="sxs-lookup"><span data-stu-id="25b8f-170">Raw SQL parameter analyzer</span></span>

<span data-ttu-id="25b8f-171">EF Core 随附新增一个代码分析器，用于检测原始 SQL API（如 `FromSql` 或 `ExecuteSqlCommand`）的潜在不安全用法。</span><span class="sxs-lookup"><span data-stu-id="25b8f-171">A new code analyzer is included with EF Core that detects potentially unsafe usages of our raw-SQL APIs, like `FromSql` or `ExecuteSqlCommand`.</span></span> <span data-ttu-id="25b8f-172">例如，对于下面的查询，将会看到一条警告，因为 minAge 未参数化  ：</span><span class="sxs-lookup"><span data-stu-id="25b8f-172">For example, for the following query, you will see a warning because _minAge_ is not parameterized:</span></span>

``` csharp
var sql = $"SELECT * FROM People WHERE Age > {minAge}";
var query = context.People.FromSql(sql);
```

## <a name="database-provider-compatibility"></a><span data-ttu-id="25b8f-173">数据库提供程序兼容性</span><span class="sxs-lookup"><span data-stu-id="25b8f-173">Database provider compatibility</span></span>

<span data-ttu-id="25b8f-174">建议配合使用 EF Core 2.1 以及已更新或至少已经过测试可用于 EF Core 2.1 的提供程序。</span><span class="sxs-lookup"><span data-stu-id="25b8f-174">It is recommended that you use EF Core 2.1 with providers that have been updated or at least tested to work with EF Core 2.1.</span></span>

> [!TIP]
> <span data-ttu-id="25b8f-175">如果新功能出现任何意外的不兼容或问题，或你有任何相关反馈，请使用[我们的问题跟踪器](https://github.com/aspnet/EntityFrameworkCore/issues/new)进行报告。</span><span class="sxs-lookup"><span data-stu-id="25b8f-175">If you find any unexpected incompatibility or any issue in the new features, or if you have feedback on them, please report it using [our issue tracker](https://github.com/aspnet/EntityFrameworkCore/issues/new).</span></span>
