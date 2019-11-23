---
title: EF4、EF5 和 EF6 的性能注意事项-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: d6d5a465-6434-45fa-855d-5eb48c61a2ea
ms.openlocfilehash: 07eb605f0d39f0c1bcfe781540525180f0dd0b22
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181677"
---
# <a name="performance-considerations-for-ef-4-5-and-6"></a>EF 4、5和6的性能注意事项
按 David Obando、Eric Dettinger 和其他

发布日期：2012年4月

上次更新时间：可能为2014

------------------------------------------------------------------------

## <a name="1-introduction"></a>1. 介绍

对象关系映射框架是一种简便的方法，可用于在面向对象的应用程序中提供数据访问的抽象。 对于 .NET 应用程序，Microsoft 推荐的 O/RM 实体框架。 但对于任何抽象，性能都可能会成为问题。

本文的目的是为了演示使用实体框架开发应用程序时的性能注意事项，使开发人员了解可能会影响性能的实体框架内部算法，并提供调查和提高使用实体框架的应用程序的性能。 Web 上已经提供了有关性能的很多好主题，还尝试尽可能指向这些资源。

性能是一个棘手的话题。 本白皮书旨在提供一种资源，帮助你为使用实体框架的应用程序做出性能相关的决策。 我们提供了一些测试指标来演示性能，但这些度量值并不是要在应用程序中看到的性能的绝对指标。

出于实用的目的，本文档假设在 .net 4.0 下运行实体框架4，实体框架5和6在 .NET 4.5 下运行。 为实体框架5所做的许多性能改进都位于 .NET 4.5 随附的核心组件中。

实体框架6是带外版本，不依赖于随 .NET 提供实体框架组件。 实体框架6同时适用于 .NET 4.0 和 .NET 4.5，并可为尚未从 .NET 4.0 升级但希望应用程序中的最新实体框架位的用户提供大性能优势。 当本文档提到实体框架6时，它是指在撰写本文时可用的最新版本：版本6.1.0。

## <a name="2-cold-vs-warm-query-execution"></a>2. 冷查询和热查询执行

第一次对给定模型进行任何查询时，实体框架在幕后执行大量工作以加载和验证模型。 我们经常将此第一个查询称为 "冷" 查询。  针对已加载模型的进一步查询称为 "热" 查询，并更快。

让我们大致了解一下使用实体框架执行查询时所用的时间，并查看在实体框架6中的改进情况。

**第一次查询执行–冷查询**

| 编写代码用户                                                                                     | 操作                    | EF4 性能影响                                                                                                                                                                                                                                                                                                                                                                                                        | EF5 性能影响                                                                                                                                                                                                                                                                                                                                                                                                                                                    | EF6 性能影响                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | 上下文创建          | 中等                                                                                                                                                                                                                                                                                                                                                                                                                        | 中等                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | 查询表达式创建 | 低                                                                                                                                                                                                                                                                                                                                                                                                                           | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var c1 = q1.First();`                                                                             | LINQ 查询执行      | -元数据加载：高但已缓存 <br/> -视图生成：可能非常高但已被缓存 <br/> -参数求值：中型 <br/> -查询转换：中型 <br/> -Materializer 生成：中型但已缓存 <br/> -数据库查询执行：可能很高 <br/> + 连接打开 <br/> + ExecuteReader <br/> + DataReader。读取 <br/> 对象具体化：中型 <br/> -Identity lookup：中型 | -元数据加载：高但已缓存 <br/> -视图生成：可能非常高但已被缓存 <br/> -参数求值：低 <br/> -查询转换：中型但已缓存 <br/> -Materializer 生成：中型但已缓存 <br/> -数据库查询执行：可能很高（某些情况下更好的查询） <br/> + 连接打开 <br/> + ExecuteReader <br/> + DataReader。读取 <br/> 对象具体化：中型 <br/> -Identity lookup：中型 | -元数据加载：高但已缓存 <br/> -视图生成：中型但已缓存 <br/> -参数求值：低 <br/> -查询转换：中型但已缓存 <br/> -Materializer 生成：中型但已缓存 <br/> -数据库查询执行：可能很高（某些情况下更好的查询） <br/> + 连接打开 <br/> + ExecuteReader <br/> + DataReader。读取 <br/> 对象具体化：中型（速度快于 EF5） <br/> -Identity lookup：中型 |
| `}`                                                                                                  | Connection.Close          | 低                                                                                                                                                                                                                                                                                                                                                                                                                           | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |


**第二个查询执行–热查询**

| 编写代码用户                                                                                     | 操作                    | EF4 性能影响                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | EF5 性能影响                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | EF6 性能影响                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | 上下文创建          | 中等                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | 中等                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | 查询表达式创建 | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var c1 = q1.First();`                                                                             | LINQ 查询执行      | -元数据~~加载~~查找：~~高但高速缓存~~低 <br/> -查看~~生成~~查找：~~可能非常高但缓存~~较低 <br/> -参数求值：中型 <br/> -查询~~转换~~查找：中型 <br/> -Materializer~~生成~~查找：~~中速但缓存~~低 <br/> -数据库查询执行：可能很高 <br/> + 连接打开 <br/> + ExecuteReader <br/> + DataReader。读取 <br/> 对象具体化：中型 <br/> -Identity lookup：中型 | -元数据~~加载~~查找：~~高但高速缓存~~低 <br/> -查看~~生成~~查找：~~可能非常高但缓存~~较低 <br/> -参数求值：低 <br/> -查询~~转换~~查找：~~中但缓存~~低 <br/> -Materializer~~生成~~查找：~~中速但缓存~~低 <br/> -数据库查询执行：可能很高（某些情况下更好的查询） <br/> + 连接打开 <br/> + ExecuteReader <br/> + DataReader。读取 <br/> 对象具体化：中型 <br/> -Identity lookup：中型 | -元数据~~加载~~查找：~~高但高速缓存~~低 <br/> -查看~~生成~~查找：~~中但缓存~~低 <br/> -参数求值：低 <br/> -查询~~转换~~查找：~~中但缓存~~低 <br/> -Materializer~~生成~~查找：~~中速但缓存~~低 <br/> -数据库查询执行：可能很高（某些情况下更好的查询） <br/> + 连接打开 <br/> + ExecuteReader <br/> + DataReader。读取 <br/> 对象具体化：中型（速度快于 EF5） <br/> -Identity lookup：中型 |
| `}`                                                                                                  | Connection.Close          | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |


可以通过多种方式降低冷查询和热查询的性能成本，我们将在下一节中介绍这些方法。 具体而言，我们将通过使用预先生成的视图来了解如何通过使用预先生成的视图来减少冷查询中模型加载的成本，这有助于减少在视图生成过程中遇到的性能难题。 对于热查询，我们将介绍查询计划缓存，无跟踪查询，以及不同的查询执行选项。

### <a name="21-what-is-view-generation"></a>2.1 什么是视图生成？

为了了解生成的视图，我们必须首先了解 "映射视图" 是什么。 映射视图是映射中为每个实体集和关联指定的转换的可执行表示形式。 在内部，这些映射视图采用 CQTs （规范查询树）的形式。 有两种类型的映射视图：

-   查询视图：这表示从数据库架构转到概念模型所需的转换。
-   更新视图：这表示从概念模型转换到数据库架构所需的转换。

请记住，概念模型可能不同于数据库架构。 例如，一个表可能用于存储两个不同实体类型的数据。 继承和非日常映射在映射视图的复杂性方面扮演着角色。

基于映射规范计算这些视图的过程是所谓的视图生成。 在加载模型时，或在生成时使用 "预生成的视图"，可以动态地执行视图生成;后者以实体 SQL 语句的形式序列化为 C\# 或 VB 文件。

当生成视图时，还会对其进行验证。 从性能角度来看，视图生成的大部分开销实际上是对视图的验证，这可以确保实体之间的连接有意义，并且具有所有支持操作的正确基数。

当执行对实体集的查询时，查询将与相应的查询视图结合使用，此组合的结果将通过计划编译器运行，以创建后备存储可以理解的查询表示形式。 对于 SQL Server，此编译的最终结果将为 T-sql SELECT 语句。 第一次执行对实体集的更新时，更新视图将通过类似的进程运行，以将其转换为目标数据库的 DML 语句。

### <a name="22-factors-that-affect-view-generation-performance"></a>影响视图生成性能的2.2 因素

视图生成步骤的性能不仅取决于模型的大小，还取决于模型的互连方式。 如果通过继承链或关联来连接两个实体，则称它们处于连接状态。 同样，如果两个表通过外键连接，它们就会连接起来。 随着架构中连接的实体和表的数量增加，视图生成成本会增加。

在最糟糕的情况下，用于生成和验证视图的算法是指数的，尽管我们确实使用某些优化来改进此功能。 对性能产生负面影响的最大因素包括：

-   模型大小，引用实体数以及这些实体之间的关联量。
-   模型复杂性，尤其是涉及大量类型的继承。
-   使用独立关联，而不是外键关联。

对于小型的简单模型，成本可能会足够小，无法使用预先生成的视图。 随着模型大小和复杂性的增加，可以使用多个选项来降低查看生成和验证的成本。

### <a name="23-using-pre-generated-views-to-decrease-model-load-time"></a>2.3 使用预先生成的视图缩短模型加载时间

有关如何使用实体框架6上预先生成的视图的详细信息，请访问[预生成的映射视图](~/ef6/fundamentals/performance/pre-generated-views.md)

#### <a name="231-pre-generated-views-using-the-entity-framework-power-tools-community-edition"></a>使用实体框架 Power Tools 社区版的2.3.1 预生成的视图

您可以使用[实体框架 6 Power Tools 社区版](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition)通过右键单击模型类文件并使用实体框架菜单选择 "生成视图" 来生成 EDMX 和 Code First 模型的视图。 实体框架 Power Tools 社区版仅适用于 DbContext 派生的上下文。

#### <a name="232-how-to-use-pre-generated-views-with-a-model-created-by-edmgen"></a>2.3.2 如何通过 Edmgen.exe 创建的模型使用预先生成的视图

Edmgen.exe 是随 .NET 提供的实用程序，与实体框架4和5（而不是实体框架6）结合使用。 Edmgen.exe 允许您从命令行生成模型文件、对象层和视图。 其中一个输出将是你选择的语言（VB 或 C）\#的视图文件。 这是一个代码文件，其中包含每个实体集的实体 SQL 代码段。 若要启用预生成的视图，只需将该文件包含在项目中。

如果手动编辑模型的架构文件，则需要重新生成视图文件。 可以通过使用 **/mode： ViewGeneration**标志运行 edmgen.exe 来实现此目的。

#### <a name="233-how-to-use-pre-generated-views-with-an-edmx-file"></a>2.3.3 如何使用带有 EDMX 文件的预生成视图

你还可以使用 Edmgen.exe 为 EDMX 文件生成视图-前面引用的 MSDN 主题介绍如何添加预先生成事件来执行此操作，但这很复杂，但在某些情况下，不可能。 通常，在模型位于 edmx 文件中时，使用 T4 模板生成视图通常更容易。

ADO.NET 团队博客中发布了一文章，介绍如何为视图生成使用 T4 模板 ( \<http://blogs.msdn.com/b/adonet/archive/2008/06/20/how-to-use-a-t4-template-for-view-generation.aspx>)。 此文章包含可下载并添加到项目中的模板。 模板是为实体框架的第一个版本而编写的，因此不能保证它们使用最新版本的实体框架。 不过，你可以为实体框架4和5from 的 Visual Studio 库下载更多最新的视图生成模板集：

-   VB.NET： \<http://visualstudiogallery.msdn.microsoft.com/118b44f2-1b91-4de2-a584-7a680418941d>
-   C\#： \<http://visualstudiogallery.msdn.microsoft.com/ae7730ce-ddab-470f-8456-1b313cd2c44d>

如果您使用的 Entity Framework 6，可以获取视图生成 T4 模板从 Visual Studio 库\<http://visualstudiogallery.msdn.microsoft.com/18a7db90-6705-4d19-9dd1-0a6c23d0751f>。

### <a name="24-reducing-the-cost-of-view-generation"></a>2.4 降低视图生成成本

使用预先生成的视图会将视图生成的成本从模型加载（运行时）移动到设计时。 虽然这在运行时提高了启动性能，但在开发时仍会遇到视图生成难点。 在编译时和运行时，有几个其他技巧可以帮助降低视图生成的成本。

#### <a name="241-using-foreign-key-associations-to-reduce-view-generation-cost"></a>2.4.1 使用外键关联降低视图生成成本

我们发现了很多情况，即从独立的关联关联到外键关联，将模型中的关联切换到外键关联大大缩短了视图生成所花费的时间。

为了演示这种改进，我们使用 Edmgen.exe 生成了两个版本的 Navision 模型。 *注意：有关 Navision 模型的说明，请参阅附录 C。* 由于在此练习中，Navision 模型的实体和关系非常多，因此它很感兴趣。

此非常大的模型的一个版本是通过外键关联生成的，另一个版本是通过独立关联生成的。 接下来，我们将为每个模型生成视图所需的时间。 实体框架5测试使用 EntityViewGenerator 类中的 GenerateViews （）方法来生成视图，而实体框架6测试使用类 StorageMappingItemCollection 中的 GenerateViews （）方法。 这是因为实体框架6代码库中发生了代码重构。

使用实体框架5，在实验室计算机中使用外键查看模型的生成耗时为65分钟。 未知的是为使用独立关联的模型生成视图所需的时间。 在实验室中，我们将运行的测试留给了在实验室中重新启动计算机以安装每月更新。

使用实体框架6，在同一实验室计算机中使用外键查看模型的生成时间为28秒。 使用独立关联的模型的视图生成花费了58秒。 对视图生成代码实体框架6进行的改进意味着许多项目不需要预生成的视图即可获得更快的启动时间。

请注意，在实体框架4和5中预先生成的视图可以通过 Edmgen.exe 或实体框架的增强工具来完成。 对于实体框架6视图，可以通过实体框架的 Power Tools 或编程方式完成，如[预生成的映射视图](~/ef6/fundamentals/performance/pre-generated-views.md)中所述。

##### <a name="2411-how-to-use-foreign-keys-instead-of-independent-associations"></a>2.4.1.1 如何使用外键，而不是独立关联

在 Visual Studio 中使用 Edmgen.exe 或 Entity Designer 时，默认情况下会获取 Fk，并只使用单个 checkbox 或命令行标志在 Fk 和 IAs 之间切换。

如果使用的是大型 Code First 模型，则使用独立关联将对视图生成产生相同的效果。 可以通过在依赖对象的类上包含外键属性来避免这种影响，尽管某些开发人员会将其视为污染其对象模型。 您可以找到这一主题的详细信息\<http://blog.oneunicorn.com/2011/12/11/whats-the-deal-with-mapping-foreign-keys-using-the-entity-framework/>。

| 当使用      | 操作步骤                                                                                                                                                                                                                                                                                                                              |
|:----------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 实体设计器 | 在两个实体之间添加关联后，请确保具有引用约束。 引用约束告诉实体框架使用外键，而不是独立关联。 有关更多详细信息，请访问\<http://blogs.msdn.com/b/efdesign/archive/2009/03/16/foreign-keys-in-the-entity-framework.aspx>。 |
| Edmgen.exe          | 当使用 Edmgen.exe 从数据库生成文件时，将遵循外键，并将其添加到模型中。 有关由 EDMGen 公开的不同选项的详细信息，请访问[http://msdn.microsoft.com/library/bb387165.aspx](https://msdn.microsoft.com/library/bb387165.aspx)。                           |
| Code First      | 有关如何在使用 Code First 时包含依赖对象的外键属性的信息，请参阅[Code First 约定](~/ef6/modeling/code-first/conventions/built-in.md)主题的 "关系约定" 部分。                                                                                              |

#### <a name="242-moving-your-model-to-a-separate-assembly"></a>2.4.2 sections 将模型移到单独的程序集

如果您的模型直接包含在您的应用程序的项目中，并通过预生成事件或 T4 模板生成视图，则在重新生成项目时，将会进行查看生成和验证，即使模型未更改也是如此。 如果将模型移到单独的程序集，并从应用程序的项目中引用它，则可以对应用程序进行其他更改，而无需重新生成包含该模型的项目。

*注意  ：* 将模型移动到不同的程序集时，请记住将模型的连接字符串复制到客户端项目的应用程序配置文件中。

#### <a name="243-disable-validation-of-an-edmx-based-model"></a>2.4.3 禁用基于 edmx 的模型的验证

在编译时验证 EDMX 模型，即使模型未更改也是如此。 如果您的模型已经过验证，则可以通过在 "属性" 窗口中将 "对生成进行验证" 属性设置为 false，在编译时取消验证。 更改映射或模型时，可以暂时重新启用验证以验证更改。

请注意，对实体框架6的 Entity Framework Designer 进行了性能改进，"生成时验证" 的成本要低于设计器的早期版本。

## <a name="3-caching-in-the-entity-framework"></a>实体框架中的3种缓存

实体框架提供以下形式的缓存内置功能：

1.  对象缓存-在 ObjectContext 实例中内置的 ObjectStateManager 将跟踪使用该实例检索的对象的内存。 这也称为第一级缓存。
2.  查询计划缓存-多次执行查询时重复使用生成的存储命令。
3.  元数据缓存-跨不同连接将模型的元数据共享到同一个模型。

除了 EF 提供的缓存外，还可以使用一种特殊类型的 ADO.NET 数据提供程序（称为包装提供程序），通过缓存为从数据库检索到的结果（也称为二级缓存）来扩展实体框架。

### <a name="31-object-caching"></a>3.1 对象缓存

默认情况下，在查询结果中返回实体时，在 EF 具体化它之前，ObjectContext 会检查是否已将具有相同键的实体加载到其 ObjectStateManager 中。 如果已存在具有相同键的实体，则 EF 会将其包含在查询结果中。 尽管 EF 仍会对数据库发出查询，但这种行为可能会绕过多个时间具体化实体的开销。

#### <a name="311-getting-entities-from-the-object-cache-using-dbcontext-find"></a>使用 DbContext 查找从对象缓存获取实体的3.1。1

与常规查询不同，DbSet 中的 Find 方法（第一次在 EF 4.1 中包含的 Api）将在内存中执行搜索，甚至对数据库发出查询。 请注意，两个不同的 ObjectContext 实例将具有两个不同的 ObjectStateManager 实例，这意味着它们具有单独的对象缓存。

查找使用主键值来尝试查找上下文跟踪的实体。 如果实体不在上下文中，则将针对数据库执行并评估查询，如果在上下文中或在数据库中找不到该实体，则返回 null。 请注意，Find 还会返回已添加到上下文中但尚未保存到数据库中的实体。

使用 "查找" 时应考虑性能。 默认情况下，对此方法的调用会触发对象缓存的验证，以检测仍处于挂起状态的对数据库的提交的更改。 如果对象缓存或要添加到对象缓存中的大型对象图中有大量对象，则此过程可能会非常昂贵，但也可以禁用。 在某些情况下，你可能会发现在禁用自动检测更改时调用 Find 方法时的差异数量级。 但当对象实际在缓存中时，以及当必须从数据库中检索对象时，还可以看到另一种数量级。 下面是一个示例图，其中使用了我们的一些 microbenchmarks （以毫秒为单位，以毫秒为单位）进行测量，负载为5000个实体：

![.Net 4.5 对数刻度](~/ef6/media/net45logscale.png ".net 4.5-对数刻度")

禁用自动检测更改的查找示例：

``` csharp
    context.Configuration.AutoDetectChangesEnabled = false;
    var product = context.Products.Find(productId);
    context.Configuration.AutoDetectChangesEnabled = true;
    ...
```

使用 Find 方法时必须考虑的事项如下：

1.  如果对象不在缓存中，则会对 Find 的优点求反，但语法仍比按键查询更简单。
2.  如果启用了自动检测更改，Find 方法的成本可能会增加一个数量级，甚至更多，具体取决于模型的复杂程度和对象缓存中的实体数量。

另外，请记住，Find 仅返回你要查找的实体，并且它不会自动加载其关联的实体（如果它们尚未在对象缓存中）。 如果需要检索关联的实体，则可以通过键进行预先加载来使用查询。 有关详细信息，请参阅**8.1 延迟加载与预先加载**。

#### <a name="312-performance-issues-when-the-object-cache-has-many-entities"></a>当对象缓存具有多个实体时，3.1.2 性能问题

对象缓存有助于增加实体框架的总体响应能力。 但是，当对象缓存加载的实体量非常大时，可能会影响某些操作，例如添加、删除、查找、条目、SaveChanges 等。 特别是，触发对 DetectChanges 的调用的操作将受到超大型对象缓存的负面影响。 DetectChanges 将对象关系图与对象状态管理器同步，其性能将由对象图的大小直接确定。 有关 DetectChanges 的详细信息，请参阅[跟踪 POCO 实体中的更改](https://msdn.microsoft.com/library/dd456848.aspx)。

当使用实体框架6时，开发人员可以直接在 DbSet 上调用 AddRange 和 RemoveRange，而不是循环访问集合并为每个实例调用一次。 使用范围方法的优点是，只为整个实体集支付一次 DetectChanges 成本，而不是每个添加的实体的费用。

### <a name="32-query-plan-caching"></a>3.2 查询计划缓存

第一次执行查询时，它会经历内部计划编译器，将概念性查询转换为存储命令（例如，在针对 SQL Server 运行时执行的 T-sql）。  如果启用了查询计划缓存，则下一次执行查询时，将直接从查询计划缓存中检索 store 命令以便执行，绕过计划编译器。

查询计划缓存在同一 AppDomain 中跨 ObjectContext 实例共享。 您无需保存到 ObjectContext 实例即可受益于查询计划缓存。

#### <a name="321-some-notes-about-query-plan-caching"></a>3.2.1 一些有关查询计划缓存的说明

-   查询计划缓存对于所有查询类型都是共享的：实体 SQL、LINQ to Entities 和 CompiledQuery 对象。
-   默认情况下，查询计划缓存针对实体 SQL 查询启用，无论是通过 EntityCommand 还是通过 ObjectQuery 执行。 默认情况下，对于 .NET 4.5 实体框架上的 LINQ to Entities 查询，以及在实体框架6中，它也处于启用状态。
    -   可以通过将 EnablePlanCaching 属性（在 EntityCommand 或 ObjectQuery 上）设置为 false 来禁用查询计划缓存。 例如：
``` csharp
                    var query = from customer in context.Customer
                                where customer.CustomerId == id
                                select new
                                {
                                    customer.CustomerId,
                                    customer.Name
                                };
                    ObjectQuery oQuery = query as ObjectQuery;
                    oQuery.EnablePlanCaching = false;
```
-   对于参数化查询，更改参数的值仍会命中缓存查询。 但更改参数的方面（例如，大小、精度或小数位数）会命中缓存中的其他条目。
-   使用实体 SQL 时，查询字符串是密钥的一部分。 更改查询会产生不同的缓存条目，即使查询在功能上是等效的。 这包括更改大小写或空白。
-   使用 LINQ 时，将对查询进行处理以生成密钥的一部分。 因此，更改 LINQ 表达式将生成不同的键。
-   其他技术限制可能适用;有关更多详细信息，请参阅 Autocompiled 查询。

#### <a name="322-cache-eviction-algorithm"></a>3.2.2 缓存逐出算法

了解内部算法的工作方式将有助于您确定何时启用或禁用查询计划缓存。 清理算法如下所示：

1.  缓存包含设置的条目数（800）后，我们会定期（每分钟一次）扫描缓存来启动一个计时器。
2.  在缓存扫描过程中，将从缓存中删除 LFRU （最不常使用的）条目。 在确定要弹出的项时，此算法会考虑命中次数和使用期限。
3.  每次缓存扫描结束时，缓存将再次包含800个条目。

确定要逐出哪些项时，将同等对待所有缓存项。 这意味着 CompiledQuery 的 store 命令与实体 SQL 查询的存储命令的逐出方式相同。

请注意，当缓存中存在800个实体时，将在中启动缓存逐出计时器，但缓存仅在此计时器启动后的60秒内进行扫描。 这意味着缓存可能会增长到相当大的60秒。

#### <a name="323-test-metrics-demonstrating-query-plan-caching-performance"></a>3.2.3 测试指标演示查询计划缓存性能

为了说明查询计划缓存对应用程序性能的影响，我们执行了测试，在此测试中，我们对 Navision 模型执行了许多实体 SQL 查询。 有关 Navision 模型的说明以及所执行的查询类型的说明，请参阅附录。 在此测试中，我们首先遍历查询列表并执行每个查询，将它们添加到缓存（如果启用了缓存）。 此步骤为 untimed。 接下来，我们使主线程睡眠超过60秒，以允许进行缓存扫描;最后，我们会循环访问该列表，第二次执行缓存的查询。 此外，在执行每组查询之前会刷新 SQL Server 计划缓存，以便我们准确地反映查询计划缓存提供的好处。

##### <a name="3231-test-results"></a>3.2.3.1 测试结果

| 测试                                                                   | EF5 无缓存 | EF5 缓存 | EF6 无缓存 | EF6 缓存 |
|:-----------------------------------------------------------------------|:-------------|:-----------|:-------------|:-----------|
| 枚举所有18723查询                                          | 124          | 125.4      | 124.3        | 125.3      |
| 避免扫描（仅限前800个查询，而不考虑复杂性）  | 41.7         | 5.5        | 40.5         | 5.4        |
| 仅限 AggregatingSubtotals 查询（total 178） | 39.5         | 4.5        | 38.1         | 4.6        |

*所有时间（以秒为单位）。*

道德-执行大量不同的查询（例如动态创建的查询）时，缓存不起作用，并且生成的缓存刷新可使从计划缓存中获益最大的查询从实际使用到它。

AggregatingSubtotals 查询是我们测试过的查询中最复杂的查询。 查询越复杂，查询计划缓存中的优势就越多。

由于 CompiledQuery 确实是缓存了其计划的 LINQ 查询，因此 CompiledQuery 与等效实体 SQL 查询的比较应具有类似的结果。 事实上，如果应用具有大量的动态实体 SQL 查询，则使用查询填充缓存也会导致 CompiledQueries 从缓存中刷新时进行 "反编译"。 在这种情况下，可以通过在动态查询上禁用缓存来设置 CompiledQueries 的优先级，从而提高性能。 当然，更好的做法是重写应用程序以使用参数化查询，而不是动态查询。

### <a name="33-using-compiledquery-to-improve-performance-with-linq-queries"></a>3.3 使用 CompiledQuery 通过 LINQ 查询提高性能

我们的测试表明，使用 CompiledQuery 可以带来 7% over autocompiled LINQ 查询的好处;这意味着，从实体框架堆栈中执行代码的时间减少了 7%;这并不意味着应用程序的速度将更快7%。 一般而言，与好处相比，在 EF 5.0 中编写和维护 CompiledQuery 对象的成本可能不值得。 你的里程可能会有所不同，因此，如果你的项目需要额外的推送，则请执行此选项。 请注意，CompiledQueries 仅与 ObjectContext 派生模型兼容，不与 DbContext 派生模型兼容。

有关创建和调用 CompiledQuery 的详细信息，请参阅[已编译的查询（LINQ to Entities）](https://msdn.microsoft.com/library/bb896297.aspx)。

在使用 CompiledQuery 时，需要考虑两个注意事项，即，要求使用静态实例和它们与可组合性相关的问题。 下面对这两个注意事项进行了深入说明。

#### <a name="331-use-static-compiledquery-instances"></a>3.3.1 使用静态 CompiledQuery 实例

因为编译 LINQ 查询是一个耗时的过程，所以我们不希望每次需要从数据库中提取数据时都要这样做。 使用 CompiledQuery 实例可以编译一次，并多次运行，但必须小心，并获得每次都重新使用同一个 CompiledQuery 实例，而不是反复编译它。 需要使用静态成员存储 CompiledQuery 实例。否则，你将看不到任何权益。

例如，假设您的页面具有以下方法主体来处理显示所选类别的产品：

``` csharp
    // Warning: this is the wrong way of using CompiledQuery
    using (NorthwindEntities context = new NorthwindEntities())
    {
        string selectedCategory = this.categoriesList.SelectedValue;

        var productsForCategory = CompiledQuery.Compile<NorthwindEntities, string, IQueryable<Product>>(
            (NorthwindEntities nwnd, string category) =>
                nwnd.Products.Where(p => p.Category.CategoryName == category)
        );

        this.productsGrid.DataSource = productsForCategory.Invoke(context, selectedCategory).ToList();
        this.productsGrid.DataBind();
    }

    this.productsGrid.Visible = true;
```

在这种情况下，每次调用该方法时，都将动态创建一个新的 CompiledQuery 实例。 每次创建新的实例时，CompiledQuery 都将经历计划编译器，而不是通过从查询计划缓存中检索存储命令来查看性能优势。 事实上，每次调用该方法时，都将使用新的 CompiledQuery 条目来污染查询计划缓存。

相反，您需要创建已编译查询的静态实例，以便在每次调用该方法时都要调用相同的编译查询。 这样做的一种方法是将 CompiledQuery 实例添加为对象上下文的成员。  然后，你可以通过 helper 方法访问 CompiledQuery 来使内容变得更干净：

``` csharp
    public partial class NorthwindEntities : ObjectContext
    {
        private static readonly Func<NorthwindEntities, string, IEnumerable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
            (NorthwindEntities context, string categoryName) =>
                context.Products.Where(p => p.Category.CategoryName == categoryName)
            );

        public IEnumerable<Product> GetProductsForCategory(string categoryName)
        {
            return productsForCategoryCQ.Invoke(this, categoryName).ToList();
        }
```

此帮助器方法将按如下方式调用：

``` csharp
    this.productsGrid.DataSource = context.GetProductsForCategory(selectedCategory);
```

#### <a name="332-composing-over-a-compiledquery"></a>3.3.2 在 CompiledQuery 上撰写

对任何 LINQ 查询进行组合的功能非常有用;为此，只需调用 IQueryable 后的方法，例如*Skip （）* 或*Count （）* 。 但这样做实际上会返回一个新的 IQueryable 对象。 尽管从技术上讲，无需对 CompiledQuery 进行撰写，但这样做会导致生成新的 IQueryable 对象，该对象要求再次通过计划编译器。

某些组件将使用组合的 IQueryable 对象来启用高级功能。 例如，ASP。NET 的 GridView 可以通过 SelectMethod 属性数据绑定到 IQueryable 对象。 然后，GridView 将通过此 IQueryable 对象组合在一起，以允许对数据模型进行排序和分页。 正如您所看到的，使用 GridView 的 CompiledQuery 不会命中已编译的查询，而是生成新的 autocompiled 查询。

在将渐进式筛选器添加到查询中时，您可能会遇到这种情况。 例如，假设有一个 "客户" 页，其中包含用于可选筛选器（例如，Country 和 OrdersCount）的多个下拉列表。 您可以在 CompiledQuery 的 IQueryable 结果中撰写这些筛选器，但这样做会导致新的查询每次执行时都会经历计划编译器。

``` csharp
    using (NorthwindEntities context = new NorthwindEntities())
    {
        IQueryable<Customer> myCustomers = context.InvokeCustomersForEmployee();

        if (this.orderCountFilterList.SelectedItem.Value != defaultFilterText)
        {
            int orderCount = int.Parse(orderCountFilterList.SelectedValue);
            myCustomers = myCustomers.Where(c => c.Orders.Count > orderCount);
        }

        if (this.countryFilterList.SelectedItem.Value != defaultFilterText)
        {
            myCustomers = myCustomers.Where(c => c.Address.Country == countryFilterList.SelectedValue);
        }

        this.customersGrid.DataSource = myCustomers;
        this.customersGrid.DataBind();
    }
```

 若要避免这种重新编译，可以重写 CompiledQuery 以考虑可能的筛选器：

``` csharp
    private static readonly Func<NorthwindEntities, int, int?, string, IQueryable<Customer>> customersForEmployeeWithFiltersCQ = CompiledQuery.Compile(
        (NorthwindEntities context, int empId, int? countFilter, string countryFilter) =>
            context.Customers.Where(c => c.Orders.Any(o => o.EmployeeID == empId))
            .Where(c => countFilter.HasValue == false || c.Orders.Count > countFilter)
            .Where(c => countryFilter == null || c.Address.Country == countryFilter)
        );
```

这会在 UI 中调用，如：

``` csharp
    using (NorthwindEntities context = new NorthwindEntities())
    {
        int? countFilter = (this.orderCountFilterList.SelectedIndex == 0) ?
            (int?)null :
            int.Parse(this.orderCountFilterList.SelectedValue);

        string countryFilter = (this.countryFilterList.SelectedIndex == 0) ?
            null :
            this.countryFilterList.SelectedValue;

        IQueryable<Customer> myCustomers = context.InvokeCustomersForEmployeeWithFilters(
                countFilter, countryFilter);

        this.customersGrid.DataSource = myCustomers;
        this.customersGrid.DataBind();
    }
```

 这里的一个折衷是，生成的存储命令将始终包含带有 null 检查的筛选器，但对于数据库服务器而言，这些筛选器应该非常简单：

``` SQL
...
WHERE ((0 = (CASE WHEN (@p__linq__1 IS NOT NULL) THEN cast(1 as bit) WHEN (@p__linq__1 IS NULL) THEN cast(0 as bit) END)) OR ([Project3].[C2] > @p__linq__2)) AND (@p__linq__3 IS NULL OR [Project3].[Country] = @p__linq__4)
```

### <a name="34-metadata-caching"></a>3.4 元数据缓存

实体框架还支持元数据缓存。 这实质上是在不同于同一模型的连接之间缓存类型信息和类型到数据库的映射信息。 元数据缓存对于每个 AppDomain 都是唯一的。

#### <a name="341-metadata-caching-algorithm"></a>3.4.1 元数据缓存算法

1.  模型的元数据信息存储在每个 EntityConnection 的 ItemCollection 中。
    -   为此，模型的不同部分都有不同的 ItemCollection 对象。 例如，StoreItemCollections 包含有关数据库模型的信息;ObjectItemCollection 包含有关数据模型的信息;EdmItemCollection 包含有关概念模型的信息。

2.  如果两个连接使用相同的连接字符串，则它们将共享同一 ItemCollection 实例。
3.  在功能上等效但以字符为不同的连接字符串可能会导致不同的元数据缓存。 我们确实标记了连接字符串，因此只需更改令牌的顺序就会产生共享元数据。 但在标记化后，两个看起来相同的连接字符串可能不会计算为相同。
4.  定期检查 ItemCollection 的使用情况。 如果确定工作区最近未访问过，则会将其标记为在下次缓存扫描时清除。
5.  仅创建 EntityConnection 将导致创建元数据缓存（尽管在打开连接之前不会对其中的项集合进行初始化）。 此工作区将保留在内存中，直到缓存算法将其确定为 "正在使用"。

客户顾问团队编写了描述保存到 ItemCollection 的引用以使用大型模型时避免"弃用"的博客文章： \<http://blogs.msdn.com/b/appfabriccat/archive/2010/10/22/metadataworkspace-reference-in-wcf-services.aspx>。

#### <a name="342-the-relationship-between-metadata-caching-and-query-plan-caching"></a>3.4.2 元数据缓存和查询计划缓存之间的关系

查询计划缓存实例驻留在 MetadataWorkspace 的存储区类型的 ItemCollection 中。 这意味着缓存的存储命令将用于针对使用给定 MetadataWorkspace 实例化的任何上下文的查询。 这也意味着，如果有两个连接字符串略有不同，并且在词汇切分后不匹配，则会有不同的查询计划缓存实例。

### <a name="35-results-caching"></a>3.5 结果缓存

使用结果缓存（也称为 "二级缓存"），可以将查询结果保存在本地缓存中。 发出查询时，先查看在对应用商店进行查询之前，结果是否可在本地使用。 尽管实体框架不直接支持结果缓存，但使用包装提供程序可以添加二级缓存。 使用二级缓存的示例包装提供程序是 Alachisoft 的[基于 NCache 实体框架二级缓存](https://www.alachisoft.com/ncache/entity-framework.html)。

此第二级缓存的实现是在计算 LINQ 表达式后发生的插入功能（和 funcletized），并且从第一级缓存计算或检索查询执行计划。 然后，第二级缓存将仅存储原始数据库结果，因此具体化管道以后仍会执行。

#### <a name="351-additional-references-for-results-caching-with-the-wrapping-provider"></a>3.5.1 用于包装提供程序的结果缓存的其他引用

-   Julie Lerman 在实体框架和 Windows Azure 中编写了一个 "二级缓存"，其中包括如何更新示例包装提供程序以使用 Windows Server AppFabric 缓存： [https://msdn.microsoft.com/magazine/hh394143.aspx](https://msdn.microsoft.com/magazine/hh394143.aspx)
-   如果您正在使用 Entity Framework 5，团队博客中发布了一文章，介绍如何使用 Entity Framework 5 的缓存提供程序运行的工作： \<http://blogs.msdn.com/b/adonet/archive/2010/09/13/ef-caching-with-jarek-kowalski-s-provider.aspx>。 它还包含 T4 模板，以帮助自动将二级缓存添加到项目。

## <a name="4-autocompiled-queries"></a>4 Autocompiled 查询

使用实体框架向数据库发出查询时，必须完成一系列步骤，然后才能真正具体化结果;一个这样的步骤就是查询编译。 已知实体 SQL 查询具有良好的性能，因为它们会自动缓存，因此，第二个或第三次执行相同的查询时，可以跳过计划编译器并改用缓存的计划。

实体框架5也为 LINQ to Entities 查询引入了自动缓存。 在过去的几个版本中实体框架创建 CompiledQuery 以提高性能，因为这会使你的 LINQ to Entities 查询可缓存。 因为缓存现在会自动执行，而不使用 CompiledQuery，因此，我们会将此功能称为 "autocompiled 查询"。 有关查询计划缓存及其机制的详细信息，请参阅查询计划缓存。

实体框架检测需要重新编译查询的时间，并在调用查询时进行调用（即使之前已编译过）。 导致查询重新编译的常见情况如下：

-   更改与查询关联的 MergeOption。 将不会使用缓存查询，而是重新运行计划编译器，并缓存新创建的计划。
-   更改 ContextOptions 的值。 UseCSharpNullComparisonBehavior。 与更改 MergeOption 的效果相同。

其他条件可能会阻止查询使用缓存。 常见示例包括：

-   使用 IEnumerable&lt;T&gt;。包含&lt;&gt;（T 值）。
-   使用生成包含常量的查询的函数。
-   使用非映射对象的属性。
-   将查询链接到需要重新编译的另一个查询。

### <a name="41-using-ienumerablelttgtcontainslttgtt-value"></a>4.1 使用 IEnumerable&lt;T&gt;。包含&lt;T&gt;（T 值）

实体框架不缓存调用 IEnumerable&lt;T&gt;的查询。包含针对内存中集合的&lt;T&gt;（T 值），因为集合的值被视为可变的。 下面的示例查询将不会被缓存，因此计划编译器将始终对其进行处理：

``` csharp
int[] ids = new int[10000];
...
using (var context = new MyContext())
{
    var query = context.MyEntities
                    .Where(entity => ids.Contains(entity.Id));

    var results = query.ToList();
    ...
}
```

请注意，执行包含的 IEnumerable 的大小决定了查询的编译速度或速度。 使用较大的集合（如上面的示例所示）时，性能可能会显著降低。

实体框架6包含对 IEnumerable&lt;T&gt;方式的优化。包含&lt;T&gt;（T 值）在执行查询时工作。 生成的 SQL 代码的速度要快得多，并且可读性更强，在大多数情况下，它在服务器上的执行速度更快。

### <a name="42-using-functions-that-produce-queries-with-constants"></a>4.2 使用生成包含常量的查询的函数

Skip （）、Take （）、Contains （）和 DefautIfEmpty （） LINQ 运算符不生成带参数的 SQL 查询，而是将作为常量传递给它们的值。 因此，除非在后续的查询执行中使用了相同的常量，否则，最终可能会完全相同的查询将污染查询计划缓存（在 EF 堆栈和数据库服务器上）。 例如：

``` csharp
var id = 10;
...
using (var context = new MyContext())
{
    var query = context.MyEntities.Select(entity => entity.Id).Contains(id);

    var results = query.ToList();
    ...
}
```

在此示例中，每次使用不同于 id 的值执行此查询时，查询都将编译为新计划。

特别要注意的是在进行分页时使用 Skip 和 Take。 在 EF6 中，这些方法具有一个 lambda 重载，可有效地使缓存查询计划可重复使用，因为 EF 可以捕获传递给这些方法的变量并将其转换为 SQLparameters。 这也有助于保留缓存清理器，因为另外，每个具有不同常量的查询都可用于 Skip，并会获取其自己的查询计划缓存条目。

请考虑以下代码，该代码是不理想的，但它只是为了求知欲此类查询：

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

此相同代码的更快版本涉及到使用 lambda 调用 Skip：

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(() => i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

由于每次运行查询时都使用相同的查询计划，因此第二个代码段的运行速度最多可达11%，这会节省 CPU 时间，并避免查询缓存的污染。 此外，由于 Skip 参数在闭包中，代码可能如下所示：

``` csharp
var i = 0;
var skippyCustomers = context.Customers.OrderBy(c => c.LastName).Skip(() => i);
for (; i < count; ++i)
{
    var currentCustomer = skippyCustomers.FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

### <a name="43-using-the-properties-of-a-non-mapped-object"></a>4.3 使用非映射对象的属性

如果查询使用非映射对象类型的属性作为参数，则不会缓存查询。 例如：

``` csharp
using (var context = new MyContext())
{
    var myObject = new NonMappedType();

    var query = from entity in context.MyEntities
                where entity.Name.StartsWith(myObject.MyProperty)
                select entity;

   var results = query.ToList();
    ...
}
```

在此示例中，假定类 NonMappedType 不是实体模型的一部分。 可以轻松地将此查询更改为不使用非映射类型，而是使用局部变量作为查询的参数：

``` csharp
using (var context = new MyContext())
{
    var myObject = new NonMappedType();
    var myValue = myObject.MyProperty;
    var query = from entity in context.MyEntities
                where entity.Name.StartsWith(myValue)
                select entity;

    var results = query.ToList();
    ...
}
```

在这种情况下，查询将能够缓存，并将受益于查询计划缓存。

### <a name="44-linking-to-queries-that-require-recompiling"></a>4.4 链接到需要重新编译的查询

按照上述示例所示，如果第二个查询依赖需要重新编译的查询，则还将重新编译整个第二个查询。 下面是一个说明此方案的示例：

``` csharp
int[] ids = new int[10000];
...
using (var context = new MyContext())
{
    var firstQuery = from entity in context.MyEntities
                        where ids.Contains(entity.Id)
                        select entity;

    var secondQuery = from entity in context.MyEntities
                        where firstQuery.Any(otherEntity => otherEntity.Id == entity.Id)
                        select entity;

    var results = secondQuery.ToList();
    ...
}
```

示例是泛型的，但它说明了链接到 firstQuery 如何导致 secondQuery 无法缓存。 如果 firstQuery 不是需要重新编译的查询，则 secondQuery 将被缓存。

## <a name="5-notracking-queries"></a>5 NoTracking 查询

### <a name="51-disabling-change-tracking-to-reduce-state-management-overhead"></a>5.1 禁用更改跟踪以减少状态管理开销

如果你处于只读方案，并且想要避免将对象加载到 ObjectStateManager 中的开销，则可以发出 "无跟踪" 查询。  可以在查询级别禁用更改跟踪。

但请注意，通过禁用更改跟踪，可以有效地关闭对象缓存。 查询实体时，无法通过从 ObjectStateManager 中拉取先前具体化的查询结果来跳过具体化。 如果在同一上下文中重复查询相同的实体，则可能会发现启用更改跟踪的性能优势。

使用 ObjectContext 进行查询时，ObjectQuery 和 ObjectSet 实例在设置后将记住 MergeOption，而在其上编写的查询将继承父查询的有效 MergeOption。 当使用 DbContext 时，可以通过对 DbSet 调用 AsNoTracking （）修饰符来禁用跟踪。

#### <a name="511-disabling-change-tracking-for-a-query-when-using-dbcontext"></a>5.1.1 在使用 DbContext 时禁用查询的更改跟踪

可以通过将对查询中的 AsNoTracking （）方法的调用链接到 NoTracking，将查询的模式切换为 NoTracking。 与 ObjectQuery 不同，DbContext API 中的 DbSet 和 DbQuery 类没有 MergeOption 的可变属性。

``` csharp
    var productsForCategory = from p in context.Products.AsNoTracking()
                                where p.Category.CategoryName == selectedCategory
                                select p;


```

#### <a name="512-disabling-change-tracking-at-the-query-level-using-objectcontext"></a>5.1.2 使用 ObjectContext 禁用查询级别的更改跟踪

``` csharp
    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;

    ((ObjectQuery)productsForCategory).MergeOption = MergeOption.NoTracking;
```

#### <a name="513-disabling-change-tracking-for-an-entire-entity-set-using-objectcontext"></a>使用 ObjectContext 禁用整个实体集的更改跟踪（5.1.3）

``` csharp
    context.Products.MergeOption = MergeOption.NoTracking;

    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;
```

### <a name="52test-metrics-demonstrating-the-performance-benefit-of-notracking-queries"></a>5.2 测试度量值演示了 NoTracking 查询的性能优势

在此测试中，我们将通过比较跟踪到 Navision 模型的 NoTracking 查询来查看填充 ObjectStateManager 的成本。 有关 Navision 模型的说明以及所执行的查询类型的说明，请参阅附录。 在此测试中，我们循环访问一个查询列表并逐个执行。 我们运行了两种不同的测试，一次是通过 NoTracking 查询，一次是 "AppendOnly" 的默认合并选项。 每个变体运行3次，并采用运行的平均值。 在测试之间，我们清除 SQL Server 上的查询缓存，并通过运行以下命令来缩减 tempdb：

1.  DBCC DROPCLEANBUFFERS
2.  DBCC FREEPROCCACHE
3.  DBCC SHRINKDATABASE （tempdb，0）

测试结果中，3个运行的中间值：

|                        | 无跟踪–工作集 | 无跟踪–时间 | 仅追加–工作集 | 仅追加–时间 |
|:-----------------------|:--------------------------|:-------------------|:--------------------------|:-------------------|
| **实体框架5** | 460361728                 | 1163536 ms         | 596545536                 | 1273042 ms         |
| **Entity Framework 6** | 647127040                 | 190228 ms          | 832798720                 | 195521 ms          |

在运行结束时，实体框架5的内存占用量要低于实体框架6。 实体框架6占用的额外内存是附加内存结构和可提高性能的代码的结果。

使用 ObjectStateManager 时，内存占用量也有明显差异。 在跟踪从数据库具体化的所有实体时，实体框架5增加了30% 的占用量。 执行此操作时，实体框架6增加了28%。

就时间而言，实体框架6在此测试中的实体框架5比大型边距高。 实体框架6在实体框架5消耗的大约16% 的时间内完成了测试。 此外，在使用 ObjectStateManager 时，实体框架5需要更多的时间来完成。 相比之下，使用 ObjectStateManager 时，实体框架6使用了3% 的时间。

## <a name="6-query-execution-options"></a>6查询执行选项

实体框架提供了几种不同的查询方法。 我们将查看以下选项，比较每个选项的优点和缺点，并检查其性能特征：

-   LINQ to Entities。
-   无跟踪 LINQ to Entities。
-   通过 ObjectQuery 的实体 SQL。
-   通过 EntityCommand 的实体 SQL。
-   System.data.objects.objectcontext.executestorequery.
-   SqlQuery.
-   CompiledQuery.

### <a name="61-linq-to-entities-queries"></a>6.1 LINQ to Entities 查询

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

**专业人员**

-   适用于 CUD 操作。
-   完全具体化的对象。
-   最简单的方式是用编程语言内置的语法编写。
-   性能良好。

**各有利弊**

-   某些技术限制，例如：
    -   对外部联接查询使用 System.linq.enumerable.defaultifempty 的模式将导致更复杂的查询，而不是实体 SQL 中的简单外部联接语句。
    -   仍不能将 LIKE 与常规模式匹配一起使用。

### <a name="62-no-tracking-linq-to-entities-queries"></a>6.2 无跟踪 LINQ to Entities 查询

当上下文派生 ObjectContext 时：

``` csharp
context.Products.MergeOption = MergeOption.NoTracking;
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

当上下文派生 DbContext：

``` csharp
var q = context.Products.AsNoTracking()
                        .Where(p => p.Category.CategoryName == "Beverages");
```

**专业人员**

-   通过常规 LINQ 查询提高了性能。
-   完全具体化的对象。
-   最简单的方式是用编程语言内置的语法编写。

**各有利弊**

-   不适用于 CUD 操作。
-   某些技术限制，例如：
    -   对外部联接查询使用 System.linq.enumerable.defaultifempty 的模式将导致更复杂的查询，而不是实体 SQL 中的简单外部联接语句。
    -   仍不能将 LIKE 与常规模式匹配一起使用。

请注意，即使未指定 NoTracking，也不会跟踪项目标量属性的查询。 例如：

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages").Select(p => new { p.ProductName });
```

此特定查询未显式指定为 NoTracking，但由于它不是对象状态管理器已知的类型，因此不会跟踪具体化的结果。

### <a name="63-entity-sql-over-an-objectquery"></a>6.3 实体 SQL ObjectQuery

``` csharp
ObjectQuery<Product> products = context.Products.Where("it.Category.CategoryName = 'Beverages'");
```

**专业人员**

-   适用于 CUD 操作。
-   完全具体化的对象。
-   支持查询计划缓存。

**各有利弊**

-   涉及与语言中内置的查询构造更容易出现用户错误的文本查询字符串。

### <a name="64-entity-sql-over-an-entity-command"></a>6.4 通过 Entity 命令实体 SQL

``` csharp
EntityCommand cmd = eConn.CreateCommand();
cmd.CommandText = "Select p From NorthwindEntities.Products As p Where p.Category.CategoryName = 'Beverages'";

using (EntityDataReader reader = cmd.ExecuteReader(CommandBehavior.SequentialAccess))
{
    while (reader.Read())
    {
        // manually 'materialize' the product
    }
}
```

**专业人员**

-   支持 .NET 4.0 中的查询计划缓存（.NET 4.5 中的所有其他查询类型都支持计划缓存）。

**各有利弊**

-   涉及与语言中内置的查询构造更容易出现用户错误的文本查询字符串。
-   不适用于 CUD 操作。
-   结果不会自动具体化，必须从数据读取器读取。

### <a name="65-sqlquery-and-executestorequery"></a>6.5 SqlQuery 和 System.data.objects.objectcontext.executestorequery

数据库上的 SqlQuery：

``` csharp
// use this to obtain entities and not track them
var q1 = context.Database.SqlQuery<Product>("select * from products");
```

DbSet 上的 SqlQuery：

``` csharp
// use this to obtain entities and have them tracked
var q2 = context.Products.SqlQuery("select * from products");
```

ExecyteStoreQuery:

``` csharp
var beverages = context.ExecuteStoreQuery<Product>(
@"     SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued, P.DiscontinuedDate
       FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
       WHERE        (C.CategoryName = 'Beverages')"
);
```

**专业人员**

-   由于计划编译器被绕过，因此速度一般最快。
-   完全具体化的对象。
-   适用于从 DbSet 中使用的 CUD 操作。

**各有利弊**

-   查询的文本和容易出错。
-   查询通过使用存储语义而不是概念语义绑定到特定的后端。
-   存在继承时，手动查询需要考虑请求类型的映射条件。

### <a name="66-compiledquery"></a>6.6 CompiledQuery

``` csharp
private static readonly Func<NorthwindEntities, string, IQueryable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
    (NorthwindEntities context, string categoryName) =>
        context.Products.Where(p => p.Category.CategoryName == categoryName)
        );
…
var q = context.InvokeProductsForCategoryCQ("Beverages");
```

**专业人员**

-   通过常规 LINQ 查询提高了7% 的性能改进。
-   完全具体化的对象。
-   适用于 CUD 操作。

**各有利弊**

-   增加了复杂性和编程开销。
-   在已编译的查询顶部编写时，性能改进会丢失。
-   某些 LINQ 查询不能编写为 CompiledQuery （例如，匿名类型的投影）。

### <a name="67-performance-comparison-of-different-query-options"></a>6.7 不同查询选项的性能比较

简单的 microbenchmarks，其中未计时上下文创建操作。 对于受控环境中的一组非缓存实体，我们测量了5000次的查询。 将会出现一条警告，其中包含警告：它们不反映应用程序产生的实际数量，而是对不同查询选项进行比较时的性能差异量的精确度量从苹果到苹果，不包括创建新上下文的成本。

| EF  | 测试                                 | 时间（毫秒） | 内存   |
|:----|:-------------------------------------|:----------|:---------|
| EF5 | ObjectContext ESQL                   | 2414      | 38801408 |
| EF5 | ObjectContext Linq 查询             | 2692      | 38277120 |
| EF5 | DbContext Linq 查询无跟踪     | 2818      | 41840640 |
| EF5 | DbContext Linq 查询                 | 2930      | 41771008 |
| EF5 | ObjectContext Linq 查询无跟踪 | 3013      | 38412288 |
|     |                                      |           |          |
| EF6 | ObjectContext ESQL                   | 2059      | 46039040 |
| EF6 | ObjectContext Linq 查询             | 3074      | 45248512 |
| EF6 | DbContext Linq 查询无跟踪     | 3125      | 47575040 |
| EF6 | DbContext Linq 查询                 | 3420      | 47652864 |
| EF6 | ObjectContext Linq 查询无跟踪 | 3593      | 45260800 |

![EF5 微基准，5000温迭代](~/ef6/media/ef5micro5000warm.png)

![EF6 微基准，5000温迭代](~/ef6/media/ef6micro5000warm.png)

Microbenchmarks 对代码中的小更改非常敏感。 在这种情况下，实体框架5与实体框架6的成本之间的差异是由于添加了[截取](~/ef6/fundamentals/logging-and-interception.md)和[事务改进](~/ef6/saving/transactions.md)。 不过，这些 microbenchmarks 数字是实体框架的作用的一小部分。 在从实体框架5升级到实体框架6时，热查询的实际方案不应看到性能回归。

为了比较不同查询选项的实际性能，我们创建了5个单独的测试变体，其中使用不同的查询选项来选择类别名称为 "饮料" 的所有产品。 每个迭代都包括创建上下文的成本，以及具体化所有返回实体的成本。 在采用1000计时迭代的总和之前，将 untimed 运行10次迭代。 显示的结果是从每个测试的5次运行中获取的中间值。 有关详细信息，请参阅包含测试代码的附录 B。

| EF  | 测试                                        | 时间（毫秒） | 内存   |
|:----|:--------------------------------------------|:----------|:---------|
| EF5 | ObjectContext 实体命令                | 621       | 39350272 |
| EF5 | 对数据库进行 DbContext Sql 查询             | 825       | 37519360 |
| EF5 | ObjectContext 存储查询                   | 878       | 39460864 |
| EF5 | ObjectContext Linq 查询无跟踪        | 969       | 38293504 |
| EF5 | 使用对象查询的 ObjectContext 实体 Sql | 1089      | 38981632 |
| EF5 | ObjectContext 编译查询                | 1099      | 38682624 |
| EF5 | ObjectContext Linq 查询                    | 1152      | 38178816 |
| EF5 | DbContext Linq 查询无跟踪            | 1208      | 41803776 |
| EF5 | DbSet 上的 DbContext Sql 查询                | 1414      | 37982208 |
| EF5 | DbContext Linq 查询                        | 1574      | 41738240 |
|     |                                             |           |          |
| EF6 | ObjectContext 实体命令                | 480       | 47247360 |
| EF6 | ObjectContext 存储查询                   | 493       | 46739456 |
| EF6 | 对数据库进行 DbContext Sql 查询             | 614       | 41607168 |
| EF6 | ObjectContext Linq 查询无跟踪        | 684       | 46333952 |
| EF6 | 使用对象查询的 ObjectContext 实体 Sql | 767       | 48865280 |
| EF6 | ObjectContext 编译查询                | 788       | 48467968 |
| EF6 | DbContext Linq 查询无跟踪            | 878       | 47554560 |
| EF6 | ObjectContext Linq 查询                    | 953       | 47632384 |
| EF6 | DbSet 上的 DbContext Sql 查询                | 1023      | 41992192 |
| EF6 | DbContext Linq 查询                        | 1290      | 47529984 |


![EF5 温查询1000迭代](~/ef6/media/ef5warmquery1000.png)

![EF6 温查询1000迭代](~/ef6/media/ef6warmquery1000.png)

> [!NOTE]
> 为完整起见，我们提供了一个变体，用于在 EntityCommand 上执行实体 SQL 查询。 但是，因为对于此类查询，结果不是具体化的，所以比较不一定是苹果。 该测试包含一个接近近似值，旨在尝试进行比较 fairer。

在这种端到端的情况下，实体框架6比实体框架5高，因为在堆栈的几个部分中进行了性能改进，包括 DbContext 初始化更轻，&lt;&gt; 查找速度更快。

## <a name="7-design-time-performance-considerations"></a>7设计时性能注意事项

### <a name="71-inheritance-strategies"></a>7.1 继承策略

使用实体框架时的另一个性能注意事项是使用的继承策略。 实体框架支持3种基本类型的继承及其组合：

-   每个层次结构一个表（TPH）–其中每个继承集都映射到一个具有鉴别器列的表，以指示该层次结构中的哪个特定类型在行中表示。
-   每种类型一个表（TPT）–其中每个类型在数据库中都有其自己的表;子表仅定义父表不包含的列。
-   每类表（TPC）–其中每个类型在数据库中都有其自己的完整表;子表定义其所有字段，包括在父类型中定义的字段。

如果模型使用 TPT 继承，则生成的查询将比使用其他继承策略生成的查询更复杂，这可能会导致存储上的执行时间较长。  它通常需要更长的时间来生成针对 TPT 模型的查询并具体化生成的对象。

请参阅"性能注意事项时在实体框架中使用 （每种类型的表） TPT 继承"MSDN 博客文章： \<http://blogs.msdn.com/b/adonet/archive/2010/08/17/performance-considerations-when-using-tpt-table-per-type-inheritance-in-the-entity-framework.aspx>。

#### <a name="711-avoiding-tpt-in-model-first-or-code-first-applications"></a>7.1.1 在 Model First 或 Code First 应用程序中避免了 TPT

在具有 TPT 架构的现有数据库上创建模型时，不会有很多选项。 但在使用 Model First 或 Code First 创建应用程序时，应避免 TPT 继承，以解决性能问题。

使用 Entity Designer 向导中 Model First 时，将获取模型中的任何继承的 TPT。 如果你想要切换到使用模型优先的 TPH 继承策略，可以使用"Entity Designer Database Generation Power Pack"可从 Visual Studio 库 ( \<http://visualstudiogallery.msdn.microsoft.com/df3541c3-d833-4b65-b942-989e7ec74c87/>)。

当使用 Code First 配置模型与继承的映射时，默认情况下，EF 将使用 TPH，因此继承层次结构中的所有实体都将映射到同一个表。 请参阅 MSDN 杂志 》 中的"代码第一个中实体 Framework4.1"项目的"映射的 Fluent API"一节 ( [http://msdn.microsoft.com/magazine/hh126815.aspx](https://msdn.microsoft.com/magazine/hh126815.aspx)) 的更多详细信息。

### <a name="72-upgrading-from-ef4-to-improve-model-generation-time"></a>7.2 从 EF4 升级以改善模型生成时间

在实体框架5和6中提供了对生成模型的应用商店层（SSDL）的算法的特定于 SQL Server 的改进，并作为在安装 Visual Studio 2010 SP1 时实体框架4的更新。 以下测试结果演示了生成非常大的模型时的改进，在本例中为 Navision 模型。 有关详细信息，请参阅附录 C。

该模型包含1005个实体集和4227个关联集。

| 配置                              | 消耗的时间细目                                                                                                                                               |
|:-------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Visual Studio 2010，实体框架4     | SSDL 生成：2小时27分钟 <br/> 映射生成：1秒 <br/> CSDL 生成：1秒 <br/> ObjectLayer 生成：1秒 <br/> 视图生成： 2 h 14 分钟 |
| Visual Studio 2010 SP1，实体框架4 | SSDL 生成：1秒 <br/> 映射生成：1秒 <br/> CSDL 生成：1秒 <br/> ObjectLayer 生成：1秒 <br/> 视图生成：1小时53分钟   |
| Visual Studio 2013，实体框架5     | SSDL 生成：1秒 <br/> 映射生成：1秒 <br/> CSDL 生成：1秒 <br/> ObjectLayer 生成：1秒 <br/> 视图生成：65分钟    |
| Visual Studio 2013，实体框架6     | SSDL 生成：1秒 <br/> 映射生成：1秒 <br/> CSDL 生成：1秒 <br/> ObjectLayer 生成：1秒 <br/> 视图生成：28秒。   |


值得注意的是，在生成 SSDL 时，负载几乎完全花费在 SQL Server 中，而客户端开发计算机等待返回的结果是从服务器返回的。 Dba 应该特别感谢这一改进。 另外，值得注意的是，在生成模型的过程中，只需立即生成模型。

### <a name="73-splitting-large-models-with-database-first-and-model-first"></a>7.3 将大型模型与 Database First 和 Model First 分离

随着模型大小的增加，设计器图面变得混乱且难以使用。 我们通常会考虑使用超过300个实体的模型太大，无法有效使用设计器。 下面的博客文章介绍了有关拆分大型模型的多个选项： \<http://blogs.msdn.com/b/adonet/archive/2008/11/25/working-with-large-models-in-entity-framework-part-2.aspx>。

张贴内容是为实体框架的第一个版本而编写的，但这些步骤仍适用。

### <a name="74-performance-considerations-with-the-entity-data-source-control"></a>7.4 实体数据源控件的性能注意事项

在多线程性能和压力测试中，我们已了解到，使用 EntityDataSource 控件的 web 应用程序的性能大大降低了这种情况。 根本原因是，EntityDataSource 对 Web 应用程序引用的程序集重复调用 LoadFromAssembly，以发现要用作实体的类型。

解决方法是将 EntityDataSource 的 ContextTypeName 设置为派生 ObjectContext 类的类型名称。 这会关闭用于扫描实体类型的所有引用程序集的机制。

设置 ContextTypeName 字段还可防止在 .NET 4.0 中的 EntityDataSource 无法通过反射从程序集加载类型时引发 ReflectionTypeLoadException 的功能问题。 此问题已在 .NET 4.5 中解决。

### <a name="75-poco-entities-and-change-tracking-proxies"></a>7.5 POCO 实体和更改跟踪代理

实体框架使你能够将自定义数据类与数据模型一起使用，而无需对数据类本身进行任何修改。 这意味着可以将“纯旧式”CLR 对象 (POCO)（例如，现有的域对象）与数据模型一起使用。 这些 POCO 数据类（也称为持久性未知对象）映射到数据模型中定义的实体，支持与实体数据模型工具生成的实体类型相同的查询、插入、更新和删除行为。

实体框架还可以创建从 POCO 类型派生的代理类，当你想要在 POCO 实体上启用延迟加载和自动更改跟踪等功能时，可以使用这些类。 POCO 类必须满足某些要求允许实体框架才能使用代理，如下所述： [http://msdn.microsoft.com/library/dd468057.aspx](https://msdn.microsoft.com/library/dd468057.aspx)。

每次实体的属性值发生更改时，机会跟踪代理都会通知对象状态管理器，因此实体框架知道实体的实际状态。 为此，可将通知事件添加到属性的 setter 方法体，并使对象状态管理器处理此类事件。 请注意，创建代理实体通常比创建非代理 POCO 实体更昂贵，因为添加了实体框架创建的事件集。

如果 POCO 实体没有更改跟踪代理，则可以通过将实体的内容与以前保存的状态的副本进行比较来找到更改。 当你在上下文中具有多个实体时，或者当你的实体具有非常多的属性（即使在上次进行比较之后它们都没有发生更改）时，此深层比较将成为一个漫长的过程。

摘要：创建更改跟踪代理时需支付性能，但当实体具有很多属性时，或在模型中有多个实体时，更改跟踪将有助于加速更改检测过程。 对于包含少量属性的实体，这些实体的实体不会增长太多，具有更改跟踪代理的好处可能并不大。

## <a name="8-loading-related-entities"></a>8加载相关实体

### <a name="81-lazy-loading-vs-eager-loading"></a>8.1 延迟加载与预先加载

实体框架提供多种不同的方法来加载与目标实体相关的实体。 例如，在查询产品时，会有不同的方法将相关订单加载到对象状态管理器中。 从性能角度来看，加载相关实体时要考虑的最重要问题是是使用延迟加载还是预先加载。

使用预先加载时，会随目标实体集一起加载相关实体。 在查询中使用 Include 语句来指示要引入的相关实体。

当使用延迟加载时，初始查询只会引入目标实体集。 但只要您访问导航属性，就会针对该存储区发出另一个查询来加载相关实体。

加载实体后，该实体的任何进一步查询都将直接从对象状态管理器加载它，无论你使用的是延迟加载还是预先加载。

### <a name="82-how-to-choose-between-lazy-loading-and-eager-loading"></a>8.2 如何在延迟加载和预先加载之间进行选择

重要的是，您了解延迟加载与预先加载之间的差异，以便您可以为应用程序做出正确的选择。 这将帮助你评估针对数据库的多个请求与可能包含大型有效负载的单个请求之间的权衡。 在应用程序的某些部分使用预先加载和其他部分中的延迟加载可能是合适的。

例如，在此期间，假设你想要查询位于英国的客户及其订单计数。

**使用预先加载**

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var ukCustomers = context.Customers.Include(c => c.Orders).Where(c => c.Address.Country == "UK");
    var chosenCustomer = AskUserToPickCustomer(ukCustomers);
    Console.WriteLine("Customer Id: {0} has {1} orders", customer.CustomerID, customer.Orders.Count);
}
```

**使用延迟加载**

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    context.ContextOptions.LazyLoadingEnabled = true;

    //Notice that the Include method call is missing in the query
    var ukCustomers = context.Customers.Where(c => c.Address.Country == "UK");

    var chosenCustomer = AskUserToPickCustomer(ukCustomers);
    Console.WriteLine("Customer Id: {0} has {1} orders", customer.CustomerID, customer.Orders.Count);
}
```

使用预先加载时，您将发出一个返回所有客户和所有订单的查询。 存储命令如下所示：

``` SQL
SELECT
[Project1].[C1] AS [C1],
[Project1].[CustomerID] AS [CustomerID],
[Project1].[CompanyName] AS [CompanyName],
[Project1].[ContactName] AS [ContactName],
[Project1].[ContactTitle] AS [ContactTitle],
[Project1].[Address] AS [Address],
[Project1].[City] AS [City],
[Project1].[Region] AS [Region],
[Project1].[PostalCode] AS [PostalCode],
[Project1].[Country] AS [Country],
[Project1].[Phone] AS [Phone],
[Project1].[Fax] AS [Fax],
[Project1].[C2] AS [C2],
[Project1].[OrderID] AS [OrderID],
[Project1].[CustomerID1] AS [CustomerID1],
[Project1].[EmployeeID] AS [EmployeeID],
[Project1].[OrderDate] AS [OrderDate],
[Project1].[RequiredDate] AS [RequiredDate],
[Project1].[ShippedDate] AS [ShippedDate],
[Project1].[ShipVia] AS [ShipVia],
[Project1].[Freight] AS [Freight],
[Project1].[ShipName] AS [ShipName],
[Project1].[ShipAddress] AS [ShipAddress],
[Project1].[ShipCity] AS [ShipCity],
[Project1].[ShipRegion] AS [ShipRegion],
[Project1].[ShipPostalCode] AS [ShipPostalCode],
[Project1].[ShipCountry] AS [ShipCountry]
FROM ( SELECT
      [Extent1].[CustomerID] AS [CustomerID],
       [Extent1].[CompanyName] AS [CompanyName],
       [Extent1].[ContactName] AS [ContactName],
       [Extent1].[ContactTitle] AS [ContactTitle],
       [Extent1].[Address] AS [Address],
       [Extent1].[City] AS [City],
       [Extent1].[Region] AS [Region],
       [Extent1].[PostalCode] AS [PostalCode],
       [Extent1].[Country] AS [Country],
       [Extent1].[Phone] AS [Phone],
       [Extent1].[Fax] AS [Fax],
      1 AS [C1],
       [Extent2].[OrderID] AS [OrderID],
       [Extent2].[CustomerID] AS [CustomerID1],
       [Extent2].[EmployeeID] AS [EmployeeID],
       [Extent2].[OrderDate] AS [OrderDate],
       [Extent2].[RequiredDate] AS [RequiredDate],
       [Extent2].[ShippedDate] AS [ShippedDate],
       [Extent2].[ShipVia] AS [ShipVia],
       [Extent2].[Freight] AS [Freight],
       [Extent2].[ShipName] AS [ShipName],
       [Extent2].[ShipAddress] AS [ShipAddress],
       [Extent2].[ShipCity] AS [ShipCity],
       [Extent2].[ShipRegion] AS [ShipRegion],
       [Extent2].[ShipPostalCode] AS [ShipPostalCode],
       [Extent2].[ShipCountry] AS [ShipCountry],
      CASE WHEN ([Extent2].[OrderID] IS NULL) THEN CAST(NULL AS int) ELSE 1 END AS [C2]
      FROM  [dbo].[Customers] AS [Extent1]
      LEFT OUTER JOIN [dbo].[Orders] AS [Extent2] ON [Extent1].[CustomerID] = [Extent2].[CustomerID]
      WHERE N'UK' = [Extent1].[Country]
)  AS [Project1]
ORDER BY [Project1].[CustomerID] ASC, [Project1].[C2] ASC
```

使用延迟加载时，将最初发出以下查询：

``` SQL
SELECT
[Extent1].[CustomerID] AS [CustomerID],
[Extent1].[CompanyName] AS [CompanyName],
[Extent1].[ContactName] AS [ContactName],
[Extent1].[ContactTitle] AS [ContactTitle],
[Extent1].[Address] AS [Address],
[Extent1].[City] AS [City],
[Extent1].[Region] AS [Region],
[Extent1].[PostalCode] AS [PostalCode],
[Extent1].[Country] AS [Country],
[Extent1].[Phone] AS [Phone],
[Extent1].[Fax] AS [Fax]
FROM [dbo].[Customers] AS [Extent1]
WHERE N'UK' = [Extent1].[Country]
```

每次访问客户的 "订单" 导航属性时，都会对该商店发出如下查询：

``` SQL
exec sp_executesql N'SELECT
[Extent1].[OrderID] AS [OrderID],
[Extent1].[CustomerID] AS [CustomerID],
[Extent1].[EmployeeID] AS [EmployeeID],
[Extent1].[OrderDate] AS [OrderDate],
[Extent1].[RequiredDate] AS [RequiredDate],
[Extent1].[ShippedDate] AS [ShippedDate],
[Extent1].[ShipVia] AS [ShipVia],
[Extent1].[Freight] AS [Freight],
[Extent1].[ShipName] AS [ShipName],
[Extent1].[ShipAddress] AS [ShipAddress],
[Extent1].[ShipCity] AS [ShipCity],
[Extent1].[ShipRegion] AS [ShipRegion],
[Extent1].[ShipPostalCode] AS [ShipPostalCode],
[Extent1].[ShipCountry] AS [ShipCountry]
FROM [dbo].[Orders] AS [Extent1]
WHERE [Extent1].[CustomerID] = @EntityKeyValue1',N'@EntityKeyValue1 nchar(5)',@EntityKeyValue1=N'AROUT'
```

有关详细信息，请参阅[加载相关对象](https://msdn.microsoft.com/library/bb896272.aspx)。

#### <a name="821-lazy-loading-versus-eager-loading-cheat-sheet"></a>8.2.1 延迟加载与预先加载备忘单

这种情况并不完全适合选择预先加载和延迟加载。 首先尝试了解两个策略之间的差异，以便您可以做出明智的决策;此外，如果你的代码符合以下任一方案，请考虑：

| 方案                                                                    | 建议                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|:----------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 是否需要从提取的实体访问多个导航属性？ | **否**-这两个选项都可能会。 但是，如果你的查询引入的负载并不太大，则使用预先加载可能会遇到性能优势，因为它需要较少的网络往返才能具体化你的对象。 <br/> <br/> **是**-如果需要从实体访问多个导航属性，则可以通过在查询中使用多个 include 语句并预先加载来实现此目的。 包含的实体越多，查询将返回的有效负载越大。 将三个或更多实体添加到查询中后，请考虑切换到延迟加载。 |
| 您是否确切知道运行时需要哪些数据？                   | **无**-延迟加载将更适合你。 否则，可能会最终查询不需要的数据。 <br/> <br/> **是**-很可能是最好的选择;它有助于更快地加载整个集。 如果查询需要提取大量数据，并且速度太慢，请改为尝试延迟加载。                                                                                                                                                                                                                                                       |
| 你的代码是否从数据库执行？ （增加网络延迟）  | **否**-当网络延迟不是问题时，使用延迟加载可能会简化你的代码。 请记住，应用程序的拓扑可能会发生更改，因此，不会对其进行已授予的数据库邻近性。 <br/> <br/> **是**-当网络出现问题时，只有你可以决定更适合你的方案。 通常，预先加载将更好，因为它需要更少的往返行程。                                                                                                                                                                                                      |


#### <a name="822-performance-concerns-with-multiple-includes"></a>多个包含8.2.2 的性能问题

当我们听到涉及服务器响应时间问题的性能问题时，问题的根源是经常用多个 Include 语句进行查询。 虽然在查询中包括相关实体的功能非常强大，但必须了解所涉及的内容。

如果查询中包含多个 Include 语句，则需要相对较长的时间来生成存储命令。 大多数情况下，尝试优化所生成的查询将花费大量时间。 对于每个包含，生成的存储命令将包含外部联接或 Union，具体取决于你的映射。 类似于这样的查询将在单个有效负载中引入数据库中的大型连接图形，这会 acerbate 任何带宽问题，尤其是当有效负载中存在大量冗余时（例如，当使用多个级别的 Include 来遍历一对多方向的关联）。

可以通过使用 ToTraceString 访问查询的基础 TSQL，并在 SQL Server Management Studio 中执行存储命令来查看负载大小，来检查查询返回的负载是否过大的情况。 在这种情况下，您可以尝试减少查询中包含语句的数量，以便只引入您需要的数据。 您也可以将查询分解成较小的子查询序列，例如：

**在中断查询之前：**

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var customers = from c in context.Customers.Include(c => c.Orders)
                    where c.LastName.StartsWith(lastNameParameter)
                    select c;

    foreach (Customer customer in customers)
    {
        ...
    }
}
```

**中断查询后：**

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var orders = from o in context.Orders
                 where o.Customer.LastName.StartsWith(lastNameParameter)
                 select o;

    orders.Load();

    var customers = from c in context.Customers
                    where c.LastName.StartsWith(lastNameParameter)
                    select c;

    foreach (Customer customer in customers)
    {
        ...
    }
}
```

这仅适用于跟踪的查询，因为我们正在使用上下文自动执行标识解析和关联修正功能的能力。

与延迟加载一样，权衡更适用于较小的负载。 你还可以使用各个属性的投影从每个实体显式选择所需的数据，但在这种情况下将不会加载实体，并且不支持更新。

#### <a name="823-workaround-to-get-lazy-loading-of-properties"></a>用于获取属性延迟加载的8.2.3 解决方法

实体框架当前不支持标量或复杂属性的延迟加载。 但是，如果您有一个包含大型对象（如 BLOB）的表，则可以使用表拆分将大属性分隔到一个单独的实体中。 例如，假设您有一个包含 varbinary photo 列的产品表。 如果不经常需要在查询中访问此属性，则可以使用表拆分来仅引入通常需要的实体部分。 表示产品照片的实体仅在明确需要时才会加载。

演示如何启用表拆分的良好资源是方便 Gil Fink 的 《 表拆分中 Entity Framework 》 博客文章： \<http://blogs.microsoft.co.il/blogs/gilf/archive/2009/10/13/table-splitting-in-entity-framework.aspx>。

## <a name="9-other-considerations"></a>9其他注意事项

### <a name="91-server-garbage-collection"></a>9.1 服务器垃圾回收

当垃圾回收器未正确配置时，某些用户可能会遇到资源争用，这些争用会限制它们期望的并行度。 每当在多线程方案中或在任何类似于服务器端系统的应用程序中使用 EF 时，请确保启用服务器垃圾回收。 这是通过应用程序配置文件中的一个简单设置来完成的：

``` xml
<?xmlversion="1.0" encoding="utf-8" ?>
<configuration>
        <runtime>
               <gcServer enabled="true" />
        </runtime>
</configuration>
```

这会减少线程争用，并增加吞吐量，使 CPU 饱和情况最多可达30%。 通常情况下，应始终使用经典垃圾回收（更好地优化 UI 和客户端方案）以及服务器垃圾回收来测试应用程序的行为。

### <a name="92-autodetectchanges"></a>9.2 AutoDetectChanges

如前文所述，当对象缓存具有多个实体时，实体框架可能会显示性能问题。 某些操作（例如添加、删除、查找、入口和 SaveChanges）触发对 DetectChanges 的调用，这些调用可能会根据对象缓存的大小而消耗大量 CPU。 出现这种情况的原因是，对象缓存和对象状态管理器尝试在执行到上下文的每个操作上尽可能保持同步，以便在各种方案中保证生成的数据是正确的。

通常，在应用程序的整个生命周期中保留实体框架的自动更改检测是一种很好的做法。 如果你的方案受到高 CPU 使用率的负面影响，并且你的配置文件指示原因是调用了 DetectChanges，请考虑在代码的敏感部分暂时关闭 AutoDetectChanges：

``` csharp
try
{
    context.Configuration.AutoDetectChangesEnabled = false;
    var product = context.Products.Find(productId);
    ...
}
finally
{
    context.Configuration.AutoDetectChangesEnabled = true;
}
```

在关闭 AutoDetectChanges 之前，最好了解这可能会导致实体框架无法跟踪有关在实体上发生的更改的特定信息。 如果处理不当，这可能会导致应用程序的数据不一致。 关闭 AutoDetectChanges 的详细信息，请阅读\<\ http://blog.oneunicorn.com/2012/03/12/secrets-of-detectchanges-part-3-switching-off-automatic-detectchanges/>。

### <a name="93-context-per-request"></a>9.3 每个请求的上下文

实体框架的上下文旨在用作生存期较短的实例，以提供最佳性能体验。 上下文应为生存期较短且被丢弃，因此，在可能的情况下将其实现为非常轻量并 reutilize 元数据。 在 web 方案中，请务必记住这一点，而不是将上下文用于单个请求的持续时间。 同样，在非 web 应用场景中，应根据你对实体框架中不同缓存级别的了解来丢弃上下文。 一般而言，应避免在应用程序的整个生命周期中使用上下文实例，以及每个线程和静态上下文的上下文。

### <a name="94-database-null-semantics"></a>9.4 数据库 null 语义

默认情况下实体框架将生成具有 C\# null 比较语义的 SQL 代码。 请参考以下示例查询：

``` csharp
            int? categoryId = 7;
            int? supplierId = 8;
            decimal? unitPrice = 0;
            short? unitsInStock = 100;
            short? unitsOnOrder = 20;
            short? reorderLevel = null;

            var q = from p incontext.Products
                    wherep.Category.CategoryName == "Beverages"
                          || (p.CategoryID == categoryId
                                || p.SupplierID == supplierId
                                || p.UnitPrice == unitPrice
                                || p.UnitsInStock == unitsInStock
                                || p.UnitsOnOrder == unitsOnOrder
                                || p.ReorderLevel == reorderLevel)
                    select p;

            var r = q.ToList();
```

在此示例中，我们将多个可以为 null 的变量与实体上的可为 null 的属性进行比较，如供应商和单价。 此查询生成的 SQL 将询问参数值与列值是否相同，或者参数和列值是否为 null。 这将隐藏数据库服务器处理 null 值的方式，并将在不同的数据库供应商之间提供一致的 C\# null 体验。 另一方面，生成的代码有点复杂，如果查询的 where 语句中的比较量增长到了很大的数字，则可能无法正常运行。

处理这种情况的一种方法是使用数据库 null 语义。 请注意，这可能会对 C\# null 语义有不同的行为，因为现在实体框架将生成更简单的 SQL，它会公开数据库引擎处理空值的方式。 对于上下文配置，可以对每个上下文激活数据库 null 语义，并提供一个配置行：

``` csharp
                context.Configuration.UseDatabaseNullSemantics = true;
```

当使用数据库 null 语义时，小型到中等大小的查询不会显示明显的性能改进，但不同之处在于具有大量可能的 null 比较的查询会变得更加明显。

在上面的示例查询中，在受控环境中运行的 microbenchmark 中，性能差异低于2%。

### <a name="95-async"></a>9.5 异步

实体框架6在 .NET 4.5 或更高版本上运行时，引入了对异步操作的支持。 大多数情况下，具有 IO 相关争用的应用程序将从使用异步查询和保存操作中获益最多。 如果你的应用程序不会受到 IO 争用的影响，则在最佳情况下，使用 async 会以同步方式运行，并以同步调用的相同时间量返回结果，或者在最坏情况下，只是将执行推迟到异步任务并添加额外的 time 到方案完成。

有关如何异步编程工作，可帮助您决定是否异步会提高应用程序的性能所访问的信息[http://msdn.microsoft.com/library/hh191443.aspx](https://msdn.microsoft.com/library/hh191443.aspx)。 有关在实体框架上使用异步操作的详细信息，请参阅[Async Query And Save](~/ef6/fundamentals/async.md
)。

### <a name="96-ngen"></a>9.6 NGEN

实体框架6不在 .NET Framework 的默认安装中。 因此，默认情况下，实体框架程序集并不是 NGEN，这意味着所有实体框架代码都服从与任何其他 MSIL 程序集相同的 JIT'ing 成本。 在生产环境中开发应用程序和冷启动应用程序时，这可能会降低 F5 体验。 为了降低 JIT'ing 的 CPU 和内存成本，建议根据需要对实体框架映像进行 NGEN。 有关如何改善实体框架6与 NGEN 的启动性能的详细信息，请参阅[通过 Ngen 改善启动性能](~/ef6/fundamentals/performance/ngen.md)。

### <a name="97-code-first-versus-edmx"></a>9.7 Code First 与 EDMX

通过内存中的概念模型（对象）、存储架构（数据库）和映射之间的映射，实体框架面向对象的编程与关系数据库之间的阻抗不匹配问题的原因。双向. 对于 short，此元数据称为实体数据模型或 EDM。 在此 EDM 中，实体框架将派生视图，以将数据从内存中的对象往返到数据库并返回数据。

如果将实体框架用于正式指定了概念模型、存储架构和映射的 EDMX 文件，则模型加载阶段仅需验证 EDM 是否正确（例如，确保没有缺少映射），然后生成视图，然后验证视图，并使此元数据可供使用。 只有这样才能执行查询，或者将新数据保存到数据存储区。

Code First 方法就是一种复杂的实体数据模型生成器。 实体框架必须从提供的代码生成 EDM;它通过分析模型中涉及的类、应用约定并通过流畅的 API 配置模型来实现此目的。 生成 EDM 后，实体框架本质上的行为与在项目中存在 EDMX 文件的方式相同。 因此，从 Code First 生成模型会增加额外的复杂性，与具有 EDMX 相比，实体框架的启动时间更慢。 成本完全取决于正在生成的模型的大小和复杂程度。

选择使用 EDMX 与 Code First 时，必须知道 Code First 引入的灵活性会增加第一次生成模型时的成本。 如果你的应用程序可以承受此首次加载的成本，则通常 Code First 将是首选方法。

## <a name="10-investigating-performance"></a>10调查性能

### <a name="101-using-the-visual-studio-profiler"></a>10.1 使用 Visual Studio 探查器

如果实体框架存在性能问题，则可以使用类似于 Visual Studio 中内置的探查器来查看应用程序所花费的时间。 这是我们用于生成饼图"探讨 ADO.NET 实体框架的第 1 部分的性能"博客文章中的工具 ( \<http://blogs.msdn.com/b/adonet/archive/2008/02/04/exploring-the-performance-of-the-ado-net-entity-framework-part-1.aspx>) ，可显示实体框架何处消耗在冷和热查询过程及其时间。

"使用 Visual Studio 2010 探查器分析实体框架" 博客文章由数据和建模客户咨询团队介绍了如何使用探查器来调查性能问题的实际示例。  \<http://blogs.msdn.com/b/dmcat/archive/2010/04/30/profiling-entity-framework-using-the-visual-studio-2010-profiler.aspx>。 此帖子是为 windows 应用程序编写的。 如果需要对 web 应用程序进行分析，Windows 性能记录器（"）和 Windows 性能分析器（WPA）工具的工作方式可能比 Visual Studio 中的更好。 WPR 和 WPA 是 Windows 性能工具包附带 Windows 评估和部署工具包的一部分 ( [http://www.microsoft.com/download/details.aspx?id=39982](https://www.microsoft.com/download/details.aspx?id=39982))。

### <a name="102-applicationdatabase-profiling"></a>10.2 应用程序/数据库分析

Visual Studio 中内置的探查器会告诉你应用程序花费时间的位置。  可以使用另一种类型的探查器，根据需要对正在生产或预生产环境中的运行中的应用程序执行动态分析，并查找数据库访问的常见缺陷和反模式。

两个地区销售的探查器是实体框架 Profiler ( \<http://efprof.com>) ORMProfiler 和 ( \<http://ormprofiler.com>)。

如果你的应用程序是使用 Code First 的 MVC 应用程序，则可以使用 Stackexchange.redis 的 MiniProfiler。 Scott Hanselman 在他的博客中介绍了此工具： \<http://www.hanselman.com/blog/NuGetPackageOfTheWeek9ASPNETMiniProfilerFromStackExchangeRocksYourWorld.aspx>。

有关分析应用程序的数据库活动的详细信息，请参阅 Julie Lerman 的 MSDN 杂志文章，其中标题[实体框架中的 "分析数据库" 活动](https://msdn.microsoft.com/magazine/gg490349.aspx)。

### <a name="103-database-logger"></a>10.3 数据库记录器

如果使用实体框架6，还应考虑使用内置日志记录功能。 可以通过简单的单行配置指示上下文的数据库属性记录其活动：

``` csharp
    using (var context = newQueryComparison.DbC.NorthwindEntities())
    {
        context.Database.Log = Console.WriteLine;
        var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
        q.ToList();
    }
```

在此示例中，数据库活动将记录到控制台中，但 Log 属性可以配置为调用&lt;字符串&gt; 委托的任何操作。

如果要在不重新编译的情况下启用数据库日志记录，并且使用实体框架6.1 或更高版本，则可以通过在应用程序的 web.config 或 app.config 文件中添加侦听器来执行此操作。

``` xml
  <interceptors>
    <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
      <parameters>
        <parameter value="C:\Path\To\My\LogOutput.txt"/>
      </parameters>
    </interceptor>
  </interceptors>
```

有关如何添加日志记录，无需重新编译转到详细信息\<http://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/>。

## <a name="11-appendix"></a>11附录

### <a name="111-a-test-environment"></a>11.1 A. 测试环境

此环境将两台计算机的安装程序与客户端应用程序单独的计算机上的数据库一起使用。 计算机位于同一机架中，因此网络延迟相对较低，但比单计算机环境更为现实。

#### <a name="1111-app-server"></a>11.1.1 应用服务器

##### <a name="11111-software-environment"></a>11.1.1.1 软件环境

-   实体框架4软件环境
    -   OS 名称： Windows Server 2008 R2 Enterprise SP1。
    -   Visual Studio 2010 –旗舰版。
    -   Visual Studio 2010 SP1 （仅适用于某些比较）。
-   实体框架5和6软件环境
    -   OS 名称： Windows 8.1 Enterprise
    -   Visual Studio 2013 –旗舰版。

##### <a name="11112-hardware-environment"></a>11.1.1.2 硬件环境

-   双处理器： Intel （R） L5520 W3530 @ 2.27 GHz，2261 Mhz8 GHz，4核，84逻辑处理器。
-   2412 GB RamRAM。
-   136 GB SCSI250GB SATA 7200 rpm 3GB/s 驱动器拆分为4个分区。

#### <a name="1112-db-server"></a>11.1.2 DB 服务器

##### <a name="11121-software-environment"></a>11.1.2.1 软件环境

-   OS 名称： Windows Server 2008 R 28.1 Enterprise SP1。
-   SQL Server 2008 R22012.

##### <a name="11122-hardware-environment"></a>11.1.2.2 硬件环境

-   单处理器： Intel （R）至强（R） CPU L5520 @ 2.27 GHz，2261 MhzES-1620 0 @ 3.60 GHz，4核，8个逻辑处理器。
-   824 GB RamRAM。
-   465 GB ATA500GB SATA 7200 rpm 6GB/s 驱动器拆分为4个分区。

### <a name="112-b-query-performance-comparison-tests"></a>11.2 b. 查询性能比较测试

Northwind 模型用于执行这些测试。 它是使用实体框架设计器从数据库生成的。 然后，使用以下代码来比较查询执行选项的性能：

``` csharp
using System;
using System.Collections.Generic;
using System.Data;
using System.Data.Common;
using System.Data.Entity.Infrastructure;
using System.Data.EntityClient;
using System.Data.Objects;
using System.Linq;

namespace QueryComparison
{
    public partial class NorthwindEntities : ObjectContext
    {
        private static readonly Func<NorthwindEntities, string, IQueryable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
            (NorthwindEntities context, string categoryName) =>
                context.Products.Where(p => p.Category.CategoryName == categoryName)
                );

        public IQueryable<Product> InvokeProductsForCategoryCQ(string categoryName)
        {
            return productsForCategoryCQ(this, categoryName);
        }
    }

    public class QueryTypePerfComparison
    {
        private static string entityConnectionStr = @"metadata=res://*/Northwind.csdl|res://*/Northwind.ssdl|res://*/Northwind.msl;provider=System.Data.SqlClient;provider connection string='data source=.;initial catalog=Northwind;integrated security=True;multipleactiveresultsets=True;App=EntityFramework'";

        public void LINQIncludingContextCreation()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {                 
                var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }

        public void LINQNoTracking()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                context.Products.MergeOption = MergeOption.NoTracking;

                var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }

        public void CompiledQuery()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                var q = context.InvokeProductsForCategoryCQ("Beverages");
                q.ToList();
            }
        }

        public void ObjectQuery()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                ObjectQuery<Product> products = context.Products.Where("it.Category.CategoryName = 'Beverages'");
                products.ToList();
            }
        }

        public void EntityCommand()
        {
            using (EntityConnection eConn = new EntityConnection(entityConnectionStr))
            {
                eConn.Open();
                EntityCommand cmd = eConn.CreateCommand();
                cmd.CommandText = "Select p From NorthwindEntities.Products As p Where p.Category.CategoryName = 'Beverages'";

                using (EntityDataReader reader = cmd.ExecuteReader(CommandBehavior.SequentialAccess))
                {
                    List<Product> productsList = new List<Product>();
                    while (reader.Read())
                    {
                        DbDataRecord record = (DbDataRecord)reader.GetValue(0);

                        // 'materialize' the product by accessing each field and value. Because we are materializing products, we won't have any nested data readers or records.
                        int fieldCount = record.FieldCount;

                        // Treat all products as Product, even if they are the subtype DiscontinuedProduct.
                        Product product = new Product();  

                        product.ProductID = record.GetInt32(0);
                        product.ProductName = record.GetString(1);
                        product.SupplierID = record.GetInt32(2);
                        product.CategoryID = record.GetInt32(3);
                        product.QuantityPerUnit = record.GetString(4);
                        product.UnitPrice = record.GetDecimal(5);
                        product.UnitsInStock = record.GetInt16(6);
                        product.UnitsOnOrder = record.GetInt16(7);
                        product.ReorderLevel = record.GetInt16(8);
                        product.Discontinued = record.GetBoolean(9);

                        productsList.Add(product);
                    }
                }
            }
        }

        public void ExecuteStoreQuery()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                ObjectResult<Product> beverages = context.ExecuteStoreQuery<Product>(
@"    SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued
    FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
    WHERE        (C.CategoryName = 'Beverages')"
);
                beverages.ToList();
            }
        }

        public void ExecuteStoreQueryDbContext()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {
                var beverages = context.Database.SqlQuery\<QueryComparison.DbC.Product>(
@"    SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued
    FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
    WHERE        (C.CategoryName = 'Beverages')"
);
                beverages.ToList();
            }
        }

        public void ExecuteStoreQueryDbSet()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {
                var beverages = context.Products.SqlQuery(
@"    SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued
    FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
    WHERE        (C.CategoryName = 'Beverages')"
);
                beverages.ToList();
            }
        }

        public void LINQIncludingContextCreationDbContext()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {                 
                var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }

        public void LINQNoTrackingDbContext()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {
                var q = context.Products.AsNoTracking().Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }
    }
}
```

### <a name="113-c-navision-model"></a>11.3 Navision 模型

Navision 数据库是用于演示 Microsoft Dynamics –导航的大型数据库。 生成的概念模型包含1005个实体集和4227个关联集。 测试中使用的模型为 "平面" –未向其添加继承。

#### <a name="1131-queries-used-for-navision-tests"></a>用于 Navision 测试的11.3.1 查询

与 Navision 模型一起使用的查询列表包含3类实体 SQL 查询：

##### <a name="11311-lookup"></a>11.3.1.1 查找

不带聚合的简单查找查询

-   计数：16232
-   示例：

``` xml
  <Query complexity="Lookup">
    <CommandText>Select value distinct top(4) e.Idle_Time From NavisionFKContext.Session as e</CommandText>
  </Query>
```

##### <a name="11312singleaggregating"></a>11.3.1.2 SingleAggregating

具有多个聚合但没有小计（单个查询）的普通 BI 查询

-   计数：2313
-   示例：

``` xml
  <Query complexity="SingleAggregating">
    <CommandText>NavisionFK.MDF_SessionLogin_Time_Max()</CommandText>
  </Query>
```

其中 MDF\_SessionLogin\_Time\_Max （）在模型中定义为：

``` xml
  <Function Name="MDF_SessionLogin_Time_Max" ReturnType="Collection(DateTime)">
    <DefiningExpression>SELECT VALUE Edm.Min(E.Login_Time) FROM NavisionFKContext.Session as E</DefiningExpression>
  </Function>
```

##### <a name="11313aggregatingsubtotals"></a>11.3.1.3 AggregatingSubtotals

具有聚合和小计的 BI 查询（通过 union all）

-   计数：178
-   示例：

``` xml
  <Query complexity="AggregatingSubtotals">
    <CommandText>
using NavisionFK;
function AmountConsumed(entities Collection([CRONUS_International_Ltd__Zone])) as
(
    Edm.Sum(select value N.Block_Movement FROM entities as E, E.CRONUS_International_Ltd__Bin as N)
)
function AmountConsumed(P1 Edm.Int32) as
(
    AmountConsumed(select value e from NavisionFKContext.CRONUS_International_Ltd__Zone as e where e.Zone_Ranking = P1)
)
----------------------------------------------------------------------------------------------------------------------
(
    select top(10) Zone_Ranking, Cross_Dock_Bin_Zone, AmountConsumed(GroupPartition(E))
    from NavisionFKContext.CRONUS_International_Ltd__Zone as E
    where AmountConsumed(E.Zone_Ranking) > @MinAmountConsumed
    group by E.Zone_Ranking, E.Cross_Dock_Bin_Zone
)
union all
(
    select top(10) Zone_Ranking, Cast(null as Edm.Byte) as P2, AmountConsumed(GroupPartition(E))
    from NavisionFKContext.CRONUS_International_Ltd__Zone as E
    where AmountConsumed(E.Zone_Ranking) > @MinAmountConsumed
    group by E.Zone_Ranking
)
union all
{
    Row(Cast(null as Edm.Int32) as P1, Cast(null as Edm.Byte) as P2, AmountConsumed(select value E
                                                                         from NavisionFKContext.CRONUS_International_Ltd__Zone as E
                                                                         where AmountConsumed(E.Zone_Ranking) > @MinAmountConsumed))
}</CommandText>
    <Parameters>
      <Parameter Name="MinAmountConsumed" DbType="Int32" Value="10000" />
    </Parameters>
  </Query>
```
