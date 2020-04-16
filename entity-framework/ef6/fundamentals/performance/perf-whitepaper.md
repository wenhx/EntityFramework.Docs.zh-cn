---
title: EF4、EF5 和 EF6 的性能注意事项 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: d6d5a465-6434-45fa-855d-5eb48c61a2ea
ms.openlocfilehash: 0ece383bb5083b41c7a2636c009473333af6d3e2
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434334"
---
# <a name="performance-considerations-for-ef-4-5-and-6"></a>EF 4、5 和 6 的性能注意事项
由大卫·奥班多，埃里克·丁格和其他人

发布时间：2012 年 4 月

上次更新时间：2014 年 5 月

------------------------------------------------------------------------

## <a name="1-introduction"></a>1. 介绍

对象关系映射框架是一种为面向对象的应用程序中的数据访问提供抽象的便捷方法。 对于 .NET 应用程序，Microsoft 推荐的 O/RM 是实体框架。 然而，对于任何抽象，性能都可能成为一个问题。

编写本白皮书是为了显示使用实体框架开发应用程序时的性能注意事项，让开发人员了解可能影响性能的实体框架内部算法，并提供调查和改进使用实体框架的应用程序的性能的提示。 Web 上已有许多有关性能的好主题，我们还尝试尽可能指向这些资源。

性能是一个棘手的话题。 本白皮书旨在作为资源，帮助您为使用实体框架的应用程序做出与性能相关的决策。 我们包括一些测试指标来演示性能，但这些指标并非旨在作为您在应用程序中看到的性能的绝对指标。

出于实际目的，本文档假定实体框架 4 在 .NET 4.0 下运行，实体框架 5 和 6 在 .NET 4.5 下运行。 实体框架 5 的许多性能改进都位于提供 .NET 4.5 的核心组件中。

实体框架 6 是带外版本，不依赖于随 .NET 一起发货的实体框架组件。 实体框架 6 在 .NET 4.0 和 .NET 4.5 上都工作，可以为尚未从 .NET 4.0 升级但希望在其应用程序中使用最新实体框架位的用户提供巨大的性能优势。 当本文档提到实体框架 6 时，它指的是本文编写时可用的最新版本：版本 6.1.0。

## <a name="2-cold-vs-warm-query-execution"></a>2. 冷与暖查询执行

首次对给定模型进行任何查询时，实体框架会在幕后执行大量工作来加载和验证模型。 我们经常将第一个查询称为"冷"查询。针对已加载模型的进一步查询称为"暖"查询，并且速度更快。

让我们从高级视图来了解使用实体框架执行查询时所花费的时间，并了解实体框架 6 中情况正在改善。

**第一次查询执行 = 冷查询**

| 代码用户写入                                                                                     | 操作                    | EF4 性能影响                                                                                                                                                                                                                                                                                                                                                                                                        | EF5 性能影响                                                                                                                                                                                                                                                                                                                                                                                                                                                    | EF6 性能影响                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | 上下文创建          | 中型                                                                                                                                                                                                                                                                                                                                                                                                                        | 中型                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | 查询表达式创建 | 低                                                                                                                                                                                                                                                                                                                                                                                                                           | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var c1 = q1.First();`                                                                             | LINQ 查询执行      | - 元数据加载：高但缓存 <br/> - 视图生成：可能非常高但缓存 <br/> - 参数评估：中等 <br/> - 查询翻译：中等 <br/> - 具体生成：中等但缓存 <br/> - 数据库查询执行：可能很高 <br/> • 连接。打开 <br/> • 命令.执行读取器 <br/> • 数据阅读器。阅读 <br/> 对象具体化：中等 <br/> - 身份查找：中等 | - 元数据加载：高但缓存 <br/> - 视图生成：可能非常高但缓存 <br/> - 参数评估：低 <br/> - 查询翻译：中等但缓存 <br/> - 具体生成：中等但缓存 <br/> - 数据库查询执行：可能很高（在某些情况下查询更好） <br/> • 连接。打开 <br/> • 命令.执行读取器 <br/> • 数据阅读器。阅读 <br/> 对象具体化：中等 <br/> - 身份查找：中等 | - 元数据加载：高但缓存 <br/> - 视图生成：中等但缓存 <br/> - 参数评估：低 <br/> - 查询翻译：中等但缓存 <br/> - 具体生成：中等但缓存 <br/> - 数据库查询执行：可能很高（在某些情况下查询更好） <br/> • 连接。打开 <br/> • 命令.执行读取器 <br/> • 数据阅读器。阅读 <br/> 对象具体化：中等（快于 EF5） <br/> - 身份查找：中等 |
| `}`                                                                                                  | 连接.关闭          | 低                                                                                                                                                                                                                                                                                                                                                                                                                           | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |


**第二次查询执行 = 暖查询**

| 代码用户写入                                                                                     | 操作                    | EF4 性能影响                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | EF5 性能影响                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | EF6 性能影响                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | 上下文创建          | 中型                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | 中型                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | 查询表达式创建 | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var c1 = q1.First();`                                                                             | LINQ 查询执行      | - 元数据~~加载~~查找：~~高但缓存~~低 <br/> - 查看~~生成~~查找：~~可能非常高但缓存~~低 <br/> - 参数评估：中等 <br/> - 查询~~翻译~~查找：中等 <br/> - 具体~~生成查找~~：~~中等但缓存~~低 <br/> - 数据库查询执行：可能很高 <br/> • 连接。打开 <br/> • 命令.执行读取器 <br/> • 数据阅读器。阅读 <br/> 对象具体化：中等 <br/> - 身份查找：中等 | - 元数据~~加载~~查找：~~高但缓存~~低 <br/> - 查看~~生成~~查找：~~可能非常高但缓存~~低 <br/> - 参数评估：低 <br/> - 查询~~翻译~~查找：~~中但缓存~~低 <br/> - 具体~~生成查找~~：~~中等但缓存~~低 <br/> - 数据库查询执行：可能很高（在某些情况下查询更好） <br/> • 连接。打开 <br/> • 命令.执行读取器 <br/> • 数据阅读器。阅读 <br/> 对象具体化：中等 <br/> - 身份查找：中等 | - 元数据~~加载~~查找：~~高但缓存~~低 <br/> - 查看~~生成~~查找：~~中等但缓存~~低 <br/> - 参数评估：低 <br/> - 查询~~翻译~~查找：~~中但缓存~~低 <br/> - 具体~~生成查找~~：~~中等但缓存~~低 <br/> - 数据库查询执行：可能很高（在某些情况下查询更好） <br/> • 连接。打开 <br/> • 命令.执行读取器 <br/> • 数据阅读器。阅读 <br/> 对象具体化：中等（快于 EF5） <br/> - 身份查找：中等 |
| `}`                                                                                                  | 连接.关闭          | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | 低                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |


有几种方法可以降低冷查询和暖查询的性能成本，我们将在下一节中介绍这些查询。 具体来说，我们将考虑通过使用预生成的视图来降低冷查询中模型加载的成本，这将有助于缓解在生成视图期间遇到的性能难题。 对于暖查询，我们将介绍查询计划缓存、无跟踪查询和不同的查询执行选项。

### <a name="21-what-is-view-generation"></a>2.1 什么是视图生成？

为了了解什么是视图生成，我们必须首先了解什么是"映射视图"。 映射视图是映射中为每个实体集和关联指定的转换的可执行表示形式。 在内部，这些映射视图采用 CQT（规范查询树）的形状。 有两种类型的映射视图：

-   查询视图：这些视图表示从数据库架构到概念模型所需的转换。
-   更新视图：这些视图表示从概念模型到数据库架构所需的转换。

请记住，概念模型可能以各种方式与数据库架构不同。 例如，一个表可用于存储两种不同实体类型的数据。 继承和非常规映射在映射视图的复杂性中起着一定的作用。

基于映射规范计算这些视图的过程就是我们所说的视图生成。 视图生成可以在加载模型时动态进行，也可以在生成时使用"预生成的视图"进行动态生成;后者以实体 SQL 语句的形式序列化到 C\#或 VB 文件。

生成视图时，也会验证视图。 从性能角度来看，视图生成成本的绝大多数实际上是对视图的验证，这可确保实体之间的连接有意义，并且对所有支持的操作具有正确的基数。

执行对实体集的查询时，查询将与相应的查询视图组合，并且此合成的结果通过计划编译器运行，以创建备份存储可以理解的查询的表示形式。 对于 SQL 服务器，此编译的最终结果将是 T-SQL SELECT 语句。 首次对实体集执行更新时，更新视图将通过类似的过程运行，以将其转换为目标数据库的 DML 语句。

### <a name="22-factors-that-affect-view-generation-performance"></a>2.2 影响视图生成性能的因素

视图生成步骤的性能不仅取决于模型的大小，还取决于模型的互连程度。 如果两个实体通过继承链或关联连接，则它们表示已连接。 同样，如果两个表通过外键连接，则它们被连接。 随着架构中连接的实体和表数量的增加，视图生成成本也会增加。

在最坏的情况下，我们用于生成和验证视图的算法呈指数级，尽管我们使用一些优化来改进这一点。 似乎对性能产生负面影响的最大因素是：

-   模型大小，指实体的数量和这些实体之间的关联量。
-   模型复杂性，特别是涉及大量类型的继承。
-   使用独立关联，而不是外国密钥关联。

对于小型、简单的模型，成本可能足够小，无需使用预生成的视图。 随着模型大小和复杂性的增加，有多种选项可用于降低视图生成和验证的成本。

### <a name="23-using-pre-generated-views-to-decrease-model-load-time"></a>2.3 使用预生成的视图减少模型加载时间

有关如何在实体框架 6 上使用预生成的视图的详细信息，请访问[预生成的映射视图](~/ef6/fundamentals/performance/pre-generated-views.md)

#### <a name="231-pre-generated-views-using-the-entity-framework-power-tools-community-edition"></a>2.3.1 使用实体框架电动工具社区版的预生成视图

您可以使用[实体框架 6 电动工具社区版](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition)生成 EDMX 和代码优先模型的视图，通过右键单击模型类文件并使用实体框架菜单选择"生成视图"。 实体框架电动工具社区版仅适用于 DbContext 派生的上下文。

#### <a name="232-how-to-use-pre-generated-views-with-a-model-created-by-edmgen"></a>2.3.2 如何使用预生成的视图与 EDMGen 创建的模型

EDMGen 是一个实用程序，它与 .NET 一起提供，并与实体框架 4 和 5 一起工作，但与实体框架 6 无关。 EDMGen 允许您从命令行生成模型文件、对象层和视图。 其中一个输出是您选择的语言的"视图"文件，VB 或 C\#。 这是一个代码文件，其中包含每个实体集的实体 SQL 代码段。 要启用预生成的视图，只需将文件包含在项目中即可。

如果手动编辑模型的架构文件，则需要重新生成视图文件。 可以通过使用 **/mode：ViewGeneration**标志运行 EDMGen 来执行此操作。

#### <a name="233-how-to-use-pre-generated-views-with-an-edmx-file"></a>2.3.3 如何使用预生成的视图与 EDMX 文件

您还可以使用 EDMGen 为 EDMX 文件生成视图 - 前面引用的 MSDN 主题介绍如何添加预构建事件来执行此操作 - 但这样做很复杂，在某些情况下，这是不可能的。 当模型位于 edmx 文件中时，通常使用 T4 模板生成视图会更容易。

ADO.NET团队博客有一篇文章，描述如何使用 T4 模板生成视图 （。 \< https://docs.microsoft.com/archive/blogs/adonet/how-to-use-a-t4-template-for-view-generation>) 此帖子包含一个模板，可以下载并添加到您的项目中。 模板是为实体框架的第一个版本编写的，因此不能保证它们能够与最新版本的实体框架配合使用。 但是，可以从可视化工作室库下载实体框架 4 和 5 的最新视图生成模板集：

-   VB.NET：\<http://visualstudiogallery.msdn.microsoft.com/118b44f2-1b91-4de2-a584-7a680418941d>
-   C\#：\<http://visualstudiogallery.msdn.microsoft.com/ae7730ce-ddab-470f-8456-1b313cd2c44d>

如果您使用的是实体框架 6，则可以从 中的\<http://visualstudiogallery.msdn.microsoft.com/18a7db90-6705-4d19-9dd1-0a6c23d0751f>可视化工作室库获取视图生成 T4 模板。

### <a name="24-reducing-the-cost-of-view-generation"></a>2.4 降低视图生成成本

使用预生成的视图可将视图生成成本从模型加载（运行时间）移动到设计时间。 虽然这提高了运行时的启动性能，但在开发时，您仍将经历视图生成的痛苦。 还有其他几种技巧可以帮助降低在编译时间和运行时生成视图的成本。

#### <a name="241-using-foreign-key-associations-to-reduce-view-generation-cost"></a>2.4.1 使用外键关联降低视图生成成本

我们看到许多案例，将模型中的关联从独立协会切换到外国密钥关联，从而大大缩短了在视图生成中花费的时间。

为了证明这一改进，我们使用 EDMGen 生成了两个版本的 Navision 模型。 *注意：有关 Navision 模型的说明，请参阅附录 C。* Navision 模型对本练习非常感兴趣，因为它拥有大量实体和它们之间的关系。

此非常大的模型的一个版本是使用外键关联生成的，另一个版本是使用独立关联生成的。 然后，我们安排为每个模型生成视图需要多长时间。 实体框架 5 测试使用类 EntityViewGenerator 的 GenerateViews（） 方法生成视图，而实体框架 6 测试使用类存储映射项目集合中的 GenerateViews（） 方法。 这是因为实体框架 6 代码库中的代码重组。

使用实体框架 5，使用外键生成模型的视图在实验室机器中花了 65 分钟。 不知道为使用独立关联的模型生成视图需要多长时间。 在实验室中重新启动计算机以安装每月更新之前，我们让测试运行了一个多月。

使用实体框架 6，使用外键生成模型的视图生成需要 28 秒在同一实验室机器中。 使用独立关联的模型的视图生成需要 58 秒。 实体框架 6 对其视图生成代码所做的改进意味着许多项目不需要预生成的视图来获取更快的启动时间。

请务必指出，实体框架 4 和 5 中的预生成视图可以使用 EDMGen 或实体框架电动工具完成。 对于实体框架 6 视图生成，可以通过实体框架电源工具完成，也可以以编程方式完成，如[预生成的映射视图](~/ef6/fundamentals/performance/pre-generated-views.md)中所述。

##### <a name="2411-how-to-use-foreign-keys-instead-of-independent-associations"></a>2.4.1.1 如何使用外键而不是独立关联

在 Visual Studio 中使用 EDMGen 或实体设计器时，默认情况下，您将获得 PK，并且只需单个复选框或命令行标志即可在 SDK 和 I 之间切换。

如果您有一个大型 Code First 模型，则使用独立关联对视图生成的影响相同。 可以通过将外键属性包括在从属对象的类中来避免这种影响，尽管一些开发人员会认为这会污染其对象模型。 您可以在 中找到\<http://blog.oneunicorn.com/2011/12/11/whats-the-deal-with-mapping-foreign-keys-using-the-entity-framework/>有关此主题的详细信息。

| 使用时      | 操作                                                                                                                                                                                                                                                                                                                              |
|:----------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 实体设计器 | 在两个实体之间添加关联后，请确保具有引用约束。 引用约束告诉实体框架使用外键而不是独立关联。 有关其他详细信息，\<https://docs.microsoft.com/archive/blogs/efdesign/foreign-keys-in-the-entity-framework>请访问 。 |
| EDMGen          | 当使用 EDMGen 从数据库生成文件时，您的外键将受到尊重并添加到模型中。 有关 EDMGen 公开的不同选项的更多信息，请访问[http://msdn.microsoft.com/library/bb387165.aspx](https://msdn.microsoft.com/library/bb387165.aspx)。                           |
| Code First      | 有关在使用代码优先时如何将外键属性包含在从属对象上的信息，请参阅[代码优先约定](~/ef6/modeling/code-first/conventions/built-in.md)主题的"关系约定"部分。                                                                                              |

#### <a name="242-moving-your-model-to-a-separate-assembly"></a>2.4.2 将模型移动到单独的装配体

当模型直接包含在应用程序的项目中，并且通过预生成事件或 T4 模板生成视图时，每当重建项目时，即使模型未更改，也会进行视图生成和验证。 如果将模型移动到单独的程序集并从应用程序的项目中引用它，则可以对应用程序进行其他更改，而无需重新生成包含模型的项目。

*注意：* 将模型移动到单独的程序集时，请记住将模型的连接字符串复制到客户端项目的应用程序配置文件中。

#### <a name="243-disable-validation-of-an-edmx-based-model"></a>2.4.3 禁用基于 edmx 的模型验证

EDMX 模型在编译时进行验证，即使模型保持不变。 如果模型已过验证，则可以通过在属性窗口中将"在生成上验证"属性设置为 false 来禁止编译时验证。 更改映射或模型时，可以临时重新启用验证以验证更改。

请注意，实体框架 6 的实体框架设计器的性能得到了提高，"在生成时验证"的成本比设计器的早期版本要低得多。

## <a name="3-caching-in-the-entity-framework"></a>3 实体框架中的缓存

实体框架具有以下形式的缓存内置：

1.  对象缓存 - 内置到 ObjectContext 实例中的 ObjectStateManager 在使用该实例检索的对象的内存中跟踪该对象。 这也称为第一级缓存。
2.  查询计划缓存 - 多次执行查询时重用生成的存储命令。
3.  元数据缓存 - 跨与同一模型的不同连接共享模型的元数据。

除了 EF 开箱即用的缓存外，一种称为包装提供程序的特殊ADO.NET数据提供程序还可用于扩展实体框架，并缓存从数据库检索的结果（也称为二级缓存）。

### <a name="31-object-caching"></a>3.1 对象缓存

默认情况下，当在查询结果中返回实体时，就在 EF 实现该实体之前，ObjectContext 将检查具有相同密钥的实体是否已加载到其 ObjectStateManager 中。 如果具有相同密钥的实体已存在，EF 将在查询结果中包括该密钥。 尽管 EF 仍将针对数据库发出查询，但此行为可以绕过实体多次实现的大部分成本。

#### <a name="311-getting-entities-from-the-object-cache-using-dbcontext-find"></a>3.1.1 使用 DbContext 查找从对象缓存获取实体

与常规查询不同，DbSet 中的 Find 方法（在 EF 4.1 中首次包含 API）将在内存中执行搜索，甚至针对数据库发出查询。 请务必注意，两个不同的 ObjectObjectContext 实例将具有两个不同的 ObjectStateManager 实例，这意味着它们具有单独的对象缓存。

Find 使用主键值尝试查找由上下文跟踪的实体。 如果实体不在上下文中，则将针对数据库执行和评估查询，如果在上下文中或数据库中找不到该实体，则返回 null。 请注意，Find 还会返回已添加到上下文但尚未保存到数据库的实体。

使用 Find 时需要考虑性能。 默认情况下，调用此方法将触发对象缓存的验证，以检测仍挂起的提交到数据库的更改。 如果对象缓存或大型对象图形中有大量对象添加到对象缓存中，此过程可能非常昂贵，但也可以禁用。 在某些情况下，在禁用自动检测更改时，在调用 Find 方法时，您可能会感觉到在调用 Find 方法时，差异程度超过一个数量级。 但是，当对象实际位于缓存中时，以及必须从数据库中检索对象时，会感知到第二个数量级。 下面是一个示例图，使用我们的一些微基准（以毫秒表示）进行测量，负载为 5000 个实体：

![.NET 4.5 对数刻度](~/ef6/media/net45logscale.png ".NET 4.5 - 对数刻度")

禁用自动检测更改的查找示例：

``` csharp
    context.Configuration.AutoDetectChangesEnabled = false;
    var product = context.Products.Find(productId);
    context.Configuration.AutoDetectChangesEnabled = true;
    ...
```

使用 Find 方法时必须考虑的是：

1.  如果对象不在缓存中，则 Find 的好处将被否定，但语法仍比按键查询更简单。
2.  如果启用自动检测更改，Find 方法的成本可能会增加一个数量级，甚至更多取决于模型的复杂性和对象缓存中的实体数量。

此外，请记住，Find 仅返回要查找的实体，如果关联实体尚未在对象缓存中，则不会自动加载它们。 如果需要检索关联的实体，可以使用按键进行查询，并进行热切加载。 有关详细信息，请参阅**8.1 延迟加载与热加载**。

#### <a name="312-performance-issues-when-the-object-cache-has-many-entities"></a>3.1.2 当对象缓存具有多个实体时的性能问题

对象缓存有助于提高实体框架的总体响应能力。 但是，当对象缓存加载的实体量非常大时，可能会影响某些操作，如添加、删除、查找、输入、保存更改等。 特别是，触发调用检测更改的操作将受到非常大的对象缓存的负面影响。 检测更改将对象图形与对象状态管理器同步，其性能将直接由对象图形的大小决定。 有关检测更改的详细信息，请参阅跟踪[POCO 实体中的更改](https://msdn.microsoft.com/library/dd456848.aspx)。

使用实体框架 6 时，开发人员可以直接在 DbSet 上调用 AddRange 和 RemoveRange，而不是在集合上迭代，并按实例调用一次 Add。 使用范围方法的优点是，检测更改的成本仅针对整个实体集支付一次，而不是每个添加的实体支付一次。

### <a name="32-query-plan-caching"></a>3.2 查询计划缓存

第一次执行查询时，它会通过内部计划编译器将概念查询转换为存储命令（例如，在对 SQL Server 运行时执行的 T-SQL）。如果启用了查询计划缓存，则下次执行查询时，将直接从查询计划缓存中检索存储命令以执行，绕过计划编译器。

查询计划缓存在同一 AppDomain 中的 ObjectContext 实例之间共享。 您无需保留 ObjectContext 实例即可从查询计划缓存中获益。

#### <a name="321-some-notes-about-query-plan-caching"></a>3.2.1 关于查询计划缓存的一些注释

-   查询计划缓存为所有查询类型共享：实体 SQL、LINQ 到实体和编译查询对象。
-   默认情况下，为实体 SQL 查询启用查询计划缓存，无论是通过实体命令还是通过对象查询执行。 默认情况下，在 .NET 4.5 上的实体框架中，在实体框架中，在实体框架中，LINQ 对实体查询也启用了它，
    -   通过将启用计划缓存属性（在实体命令或对象查询上）设置为 false，可以禁用查询计划缓存。 例如：
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
-   对于参数化查询，更改参数的值仍将命中缓存的查询。 但是，更改参数的分面（例如，大小、精度或比例）将击中缓存中的不同条目。
-   使用实体 SQL 时，查询字符串是密钥的一部分。 更改查询将会导致不同的缓存条目，即使查询在功能上等效。 这包括对套管或空白的更改。
-   使用 LINQ 时，将处理查询以生成密钥的一部分。 因此，更改 LINQ 表达式将生成不同的键。
-   可能适用其他技术限制;有关详细信息，请参阅自动编译查询。

#### <a name="322-cache-eviction-algorithm"></a>3.2.2 缓存逐出算法

了解内部算法的工作原理将帮助您确定何时启用或禁用查询计划缓存。 清理算法如下：

1.  一旦缓存包含一组条目数 （800），我们将启动一个计时器，定期（每分钟一次）扫描缓存。
2.  在缓存扫描期间，条目会以 LFRU（最不频繁和最近使用）为基础从缓存中删除。 此算法在决定弹出哪些条目时，会考虑命中计数和年龄。
3.  在每个缓存扫描结束时，缓存再次包含 800 个条目。

在确定要驱逐的条目时，所有缓存条目都一视同仁。 这意味着编译查询的存储命令与实体 SQL 查询的存储命令具有相同的逐出机会。

请注意，缓存驱逐计时器在缓存中有 800 个实体时被踢中，但缓存仅在启动此计时器 60 秒后被扫描。 这意味着，最多 60 秒，您的缓存可能会增长到相当大。

#### <a name="323-test-metrics-demonstrating-query-plan-caching-performance"></a>3.2.3 演示查询计划缓存性能的测试指标

为了演示查询计划缓存对应用程序性能的影响，我们执行了一个测试，其中针对 Navision 模型执行了许多实体 SQL 查询。 有关 Navision 模型的说明以及已执行的查询类型，请参阅附录。 在此测试中，我们首先迭代查询列表，并执行每个查询一次以将其添加到缓存（如果启用了缓存）。 此步骤是非时无刻的。 接下来，我们将主线程休眠超过 60 秒，以便进行缓存扫描;最后，我们第二次遍通列表以执行缓存的查询。 此外，在执行每组查询之前刷新 SQL Server 计划缓存，以便我们获取的时间准确反映查询计划缓存提供的好处。

##### <a name="3231-test-results"></a>3.2.3.1 测试结果

| 测试                                                                   | EF5 无缓存 | EF5 缓存 | EF6 无缓存 | EF6 缓存 |
|:-----------------------------------------------------------------------|:-------------|:-----------|:-------------|:-----------|
| 枚举所有 18723 个查询                                          | 124          | 125.4      | 124.3        | 125.3      |
| 避免扫描（仅前 800 个查询，无论复杂性如何）  | 41.7         | 5.5        | 40.5         | 5.4        |
| 仅聚合子总计查询（总计 178 个 - 避免扫描） | 39.5         | 4.5        | 38.1         | 4.6        |

*所有时间（以秒为单位）。*

道德 - 在执行大量不同的查询（例如，动态创建的查询）时，缓存不起作用，并且生成的缓存刷新可以使从计划缓存中从实际使用中受益最大的查询保持。

聚合子总计查询是测试的查询中最复杂的。 正如预期的那样，查询越复杂，您从查询计划缓存中看到的好处就越大。

由于编译查询实际上是一个 LINQ 查询，其计划缓存，因此编译查询与等效实体 SQL 查询的比较应具有类似的结果。 事实上，如果应用具有大量动态实体 SQL 查询，则使用查询填充缓存也会有效地导致编译查询在从缓存中刷新时"去编译"。 在这种情况下，可以通过禁用动态查询的缓存来确定编译查询的优先级，从而提高性能。 当然，更好的是重写应用以使用参数化查询而不是动态查询。

### <a name="33-using-compiledquery-to-improve-performance-with-linq-queries"></a>3.3 使用编译查询提高 LINQ 查询的性能

我们的测试表明，使用编译查询可以带来 7% 的好处比自动编译的 LINQ 查询;这意味着您将在实体框架堆栈中执行代码的时间减少 7%;这并不意味着您的应用程序将加快 7%。 一般来说，在 EF 5.0 中写入和维护编译查询对象的成本与收益相比可能不值得麻烦。 您的里程可能会有所不同，因此，如果您的项目需要额外的推送，请执行此选项。 请注意，编译查询仅与对象上下文派生模型兼容，并且与 DbContext 派生的模型不兼容。

有关创建和调用编译查询的详细信息，请参阅[编译查询（LINQ 到实体）。](https://msdn.microsoft.com/library/bb896297.aspx)

使用 Compilequery 时，您需要考虑两个注意事项，即使用静态实例的要求及其可组合性问题。 下面对这两个注意事项进行了深入解释。

#### <a name="331-use-static-compiledquery-instances"></a>3.3.1 使用静态编译查询实例

由于编译 LINQ 查询是一个耗时的过程，因此我们不希望每次需要从数据库获取数据时都这样做。 编译查询实例允许您编译一次并运行多次，但您必须小心并采购，以便每次都重复使用同一个编译查询实例，而不是反复编译它。 使用静态成员来存储编译查询实例是必要的;否则，您不会看到任何好处。

例如，假设您的页面具有以下方法正文来处理显示所选类别的产品：

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

在这种情况下，每次调用方法时，您都会动态创建新的编译查询实例。 CompiledQuery 不会通过从查询计划缓存中检索存储命令来看到性能优势，而是每次创建新实例时都会通过计划编译器。 事实上，每次调用该方法时，您都会使用新的编译查询条目来污染查询计划缓存。

相反，您希望创建已编译查询的静态实例，以便每次调用方法时都调用相同的已编译查询。 这样做的一种方法是添加编译查询实例作为对象上下文的成员。然后，您可以通过帮助器方法访问编译查询，使事情变得更干净一些：

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

此帮助器方法将调用如下：

``` csharp
    this.productsGrid.DataSource = context.GetProductsForCategory(selectedCategory);
```

#### <a name="332-composing-over-a-compiledquery"></a>3.3.2 通过编译查询进行组合

通过任何 LINQ 查询进行撰写的能力非常有用;为此，只需在*IQuery（）* 或*Count（）* 之后调用方法。 但是，这样做实质上会返回一个新的可查询对象。 虽然在技术上没有什么可以阻止您通过 CompiledQuery 进行创作，但这样做将导致生成需要再次通过计划编译器的新 IQuery 对象。

某些组件将使用组合的 IQuery 对象来实现高级功能。 例如，ASP。NET 的 GridView 可以通过 SelectMethod 属性将数据绑定到 IQuery 对象。 然后，GridView 将在此可查询对象上进行组合，以允许对数据模型进行排序和分页。 如您所见，使用 GridView 的编译查询不会命中已编译的查询，而是生成新的自动编译查询。

在一个可能遇到这种情况的地方是向查询添加累进筛选器时。 例如，假设您有一个客户页面，该页面包含多个可选筛选器的下拉列表（例如，国家/地区订单和订单计数）。 您可以在编译查询的 IQuery 可查询结果上编写这些筛选器，但这样做将导致每次执行计划编译器时新查询都会经过计划编译器。

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

 为了避免此重新编译，您可以重写编译查询，以考虑可能的筛选器：

``` csharp
    private static readonly Func<NorthwindEntities, int, int?, string, IQueryable<Customer>> customersForEmployeeWithFiltersCQ = CompiledQuery.Compile(
        (NorthwindEntities context, int empId, int? countFilter, string countryFilter) =>
            context.Customers.Where(c => c.Orders.Any(o => o.EmployeeID == empId))
            .Where(c => countFilter.HasValue == false || c.Orders.Count > countFilter)
            .Where(c => countryFilter == null || c.Address.Country == countryFilter)
        );
```

将在 UI 中调用，例如：

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

 此处的权衡是生成的存储命令将始终具有带有空检查的筛选器，但对于数据库服务器来说，优化这些筛选器应该相当简单：

``` SQL
...
WHERE ((0 = (CASE WHEN (@p__linq__1 IS NOT NULL) THEN cast(1 as bit) WHEN (@p__linq__1 IS NULL) THEN cast(0 as bit) END)) OR ([Project3].[C2] > @p__linq__2)) AND (@p__linq__3 IS NULL OR [Project3].[Country] = @p__linq__4)
```

### <a name="34-metadata-caching"></a>3.4 元数据缓存

实体框架还支持元数据缓存。 这实质上是跨不同连接到同一模型的类型信息和类型到数据库映射信息。 元数据缓存对于每个 AppDomain 是唯一的。

#### <a name="341-metadata-caching-algorithm"></a>3.4.1 元数据缓存算法

1.  模型的元数据信息存储在每个实体连接的项目集合中。
    -   作为旁注，模型的不同部分有不同的 ItemCollection 对象。 例如，StoreItem集合包含有关数据库模型的信息;因此，它包含有关数据库模型的信息。ObjectItemCollection 包含有关数据模型的信息;EdmItemCollection 包含有关概念模型的信息。

2.  如果两个连接使用相同的连接字符串，它们将共享相同的 ItemCollection 实例。
3.  功能上等效，但文本上不同的连接字符串可能会导致不同的元数据缓存。 我们使用令牌化连接字符串，因此只需更改令牌的顺序即可生成共享元数据。 但是，在标记化后，两个在功能上看起来相同的连接字符串可能不会被计算为相同。
4.  定期检查项目集合以表示使用。 如果确定最近未访问工作区，则将在下一次缓存扫描时将其标记为清理。
5.  仅仅创建实体连接就会导致创建元数据缓存（尽管在打开连接之前不会初始化其中的项集合）。 此工作区将保留在内存中，直到缓存算法确定它不"正在使用"。

客户咨询团队撰写了一篇博客文章，其中描述了对 ItemCollection 的引用，以避免在使用大型模型时"弃用"： \< https://docs.microsoft.com/archive/blogs/appfabriccat/holding-a-reference-to-the-ef-metadataworkspace-for-wcf-services>。

#### <a name="342-the-relationship-between-metadata-caching-and-query-plan-caching"></a>3.4.2 元数据缓存和查询计划缓存之间的关系

查询计划缓存实例位于元数据工作区的存储类型的项目集合中。 这意味着缓存存储命令将用于查询使用给定元数据工作区实例化的任何上下文。 这也意味着，如果您有两个连接字符串略有不同，在标记后不匹配，则将具有不同的查询计划缓存实例。

### <a name="35-results-caching"></a>3.5 结果缓存

使用结果缓存（也称为"第二级缓存"），您将查询的结果保存在本地缓存中。 发出查询时，首先在查询存储之前，先查看结果是否在本地可用。 虽然实体框架并不直接支持结果缓存，但可以使用包装提供程序添加第二级缓存。 一个带有第二级缓存的包装提供程序的示例是 Alachisoft[基于 NCache 的实体框架第二级缓存](https://www.alachisoft.com/ncache/entity-framework.html)。

二级缓存的实现是在 LINQ 表达式计算（和 fee）并从第一级缓存中计算或检索查询执行计划之后发生的注入功能。 然后，第二级缓存将仅存储原始数据库结果，因此具体化管道在之后仍执行。

#### <a name="351-additional-references-for-results-caching-with-the-wrapping-provider"></a>3.5.1 使用包装提供程序进行结果缓存的其他引用

-   Julie Lerman 撰写了一篇"实体框架和 Windows Azure 中的二级缓存"MSDN 文章，其中包括如何更新示例包装提供程序以使用 Windows Server AppFabric 缓存：[https://msdn.microsoft.com/magazine/hh394143.aspx](https://msdn.microsoft.com/magazine/hh394143.aspx)
-   如果您正在使用实体框架 5，则团队博客有一篇文章，说明如何使用实体框架 5 的缓存提供程序运行内容： \< https://docs.microsoft.com/archive/blogs/adonet/ef-caching-with-jarek-kowalskis-provider>。 它还包括 T4 模板，以帮助自动将二级缓存添加到项目中。

## <a name="4-autocompiled-queries"></a>4 自动编译查询

使用实体框架对数据库发出查询时，它必须经过一系列步骤才能实际实现结果;其中一个步骤是查询编译。 实体 SQL 查询在自动缓存时具有良好的性能，因此，在进行同一查询的第二次或第三次时，它可以跳过计划编译器并使用缓存的计划。

实体框架 5 还引入了 LINQ 到实体查询的自动缓存。 在以往版本的实体框架中，创建编译查询以加快性能是一种常见做法，因为这将使 LINQ 到实体查询可缓存。 由于缓存现在无需使用编译查询即可自动完成，因此我们将此功能称为"自动编译查询"。 有关查询计划缓存及其机制的详细信息，请参阅查询计划缓存。

实体框架检测查询何时需要重新编译，并且在调用查询时（即使以前已编译）也执行。 导致重新编译查询的常见条件是：

-   更改与您的查询关联的合并选项。 将不会使用缓存的查询，而是计划编译器将再次运行，并缓存新创建的计划。
-   更改上下文选项的值。 您将获得与更改合并选项相同的效果。

其他条件可能会阻止查询使用缓存。 常见示例包括：

-   使用 IE500T&lt;&gt;。包含&lt;&gt;（T 值）。
-   使用生成具有常量的查询的函数。
-   使用非映射对象的属性。
-   将查询链接到需要重新编译的另一个查询。

### <a name="41-using-ienumerablelttgtcontainslttgtt-value"></a>4.1 使用 IE500T&lt;。&gt;包含&lt;T（T&gt;值）

实体框架不缓存调用 IE55T&lt;的&gt;查询。包含&lt;针对内存&gt;中集合的 T（T 值），因为集合的值被视为易失性。 以下示例查询将不会缓存，因此计划编译器将始终对其进行处理：

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

请注意，执行"包含"的 IE55 的大小的确定查询的编译速度或速度。 使用大型集合（如上例中所示的集合）时，性能可能会受到显著影响。

实体框架 6 包含对 IE50t&lt;T&gt;方式的优化。在执行查询&lt;时&gt;，包含 T（T 值）工作。 生成的 SQL 代码生成速度要快得多，而且可读性更强，在大多数情况下，它还在服务器中执行得更快。

### <a name="42-using-functions-that-produce-queries-with-constants"></a>4.2 使用生成具有常量的查询的函数

Skip（）、Take（）、包含（）和 DefautIfEmpty（） LINQ 运算符不生成具有参数的 SQL 查询，而是将传递给它们的值作为常量传递给它们。 因此，否则可能相同的查询最终会污染 EF 堆栈和数据库服务器上的查询计划缓存，除非在后续查询执行中使用相同的常量，否则不会重新使用。 例如：

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

在此示例中，每次执行此查询时，使用 ID 的不同值，查询将编译为新计划。

在进行分页时，尤其要注意使用跳过和取。 在 EF6 中，这些方法具有 lambda 重载，可有效地使缓存的查询计划可重用，因为 EF 可以捕获传递给这些方法的变量并将其转换为 SQL 参数。 这也有助于保持缓存更干净，否则每个查询使用不同的常量为 Skip 和 Take 将获得其自己的查询计划缓存条目。

请考虑以下代码，该代码不理想，但仅用于举例说明此类查询：

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

此代码的更快版本将涉及调用使用 lambda 的跳过：

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(() => i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

第二个代码段的运行速度可能加快 11%，因为每次运行查询时都使用相同的查询计划，从而节省了 CPU 时间并避免污染查询缓存。 此外，由于 Skip 的参数位于闭包中，因此代码现在可能如下所示：

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

当查询使用非映射对象类型的属性作为参数时，查询将不会被缓存。 例如：

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

在此示例中，假设类非映射类型不是实体模型的一部分。 可以轻松地更改此查询，以不使用非映射类型，而是使用本地变量作为查询的参数：

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

在这种情况下，查询将能够获得缓存，并从查询计划缓存中获益。

### <a name="44-linking-to-queries-that-require-recompiling"></a>4.4 链接到需要重新编译的查询

按照上述相同的示例，如果您有第二个依赖于需要重新编译的查询，则整个第二个查询也将重新编译。 下面是一个示例来说明此方案：

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

该示例是泛型的，但它说明了链接到第一查询如何导致第二查询无法缓存。 如果第一查询不是需要重新编译的查询，则第二查询将被缓存。

## <a name="5-notracking-queries"></a>5 无跟踪查询

### <a name="51-disabling-change-tracking-to-reduce-state-management-overhead"></a>5.1 禁用更改跟踪以减少状态管理开销

如果您处于只读方案，并且希望避免将对象加载到 ObjectStateManager 中的开销，则可以发出"无跟踪"查询。可以在查询级别禁用更改跟踪。

但请注意，通过禁用更改跟踪，您实际上正在关闭对象缓存。 当您查询实体时，我们不能通过从 ObjectStateManager 提取以前具体化的查询结果来跳过具体化。 如果在同一上下文中反复查询相同的实体，则实际上可能会看到启用更改跟踪的性能优势。

使用 ObjectContext 查询时，ObjectQuery 和 ObjectSet 实例将在设置合并选项后记住它，并且在它们上组成的查询将继承父查询的有效 MergeOption。 使用 DbContext 时，可以通过在 DbSet 上调用 AsNo 跟踪（） 修改器来禁用跟踪。

#### <a name="511-disabling-change-tracking-for-a-query-when-using-dbcontext"></a>5.1.1 禁用使用 DbContext 时查询的更改跟踪

通过将调用链接到查询中的 AsNoTrack（） 方法，可以将查询模式切换到 NoTrack。 与对象查询不同，DbContext API 中的 DbSet 和 DbQuery 类没有 MergeOption 的可变属性。

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

#### <a name="513-disabling-change-tracking-for-an-entire-entity-set-using-objectcontext"></a>5.1.3 使用 ObjectContext 禁用整个实体集的更改跟踪

``` csharp
    context.Products.MergeOption = MergeOption.NoTracking;

    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;
```

### <a name="52test-metrics-demonstrating-the-performance-benefit-of-notracking-queries"></a>5.2 测试指标，演示 NoTrack 查询的性能优势

在此测试中，我们将通过将跟踪与 Navision 模型的 NoTrack 查询进行比较来查看填充 ObjectStateManager 的成本。 有关 Navision 模型的说明以及已执行的查询类型，请参阅附录。 在此测试中，我们迭代查询列表并执行每个查询一次。 我们运行了两个测试变体，一次使用 NoTrack 查询，另一次使用默认合并选项"仅追加"。 我们运行每个变体 3 次，并获取运行的平均值。 在测试之间，我们清除 SQL Server 上的查询缓存，并通过运行以下命令来收缩 tempdb：

1.  DBCC DROPCLEANBUFFERS
2.  DBCC FREEPROCCACHE
3.  DBCC SHRINK 数据库（tempdb， 0）

测试结果，3 次以上的中值：

|                        | 无跟踪 + 工作集 | 无跟踪 + 时间 | 仅追加 = 工作集 | 仅追加 = 时间 |
|:-----------------------|:--------------------------|:-------------------|:--------------------------|:-------------------|
| **实体框架 5** | 460361728                 | 1163536 毫秒         | 596545536                 | 1273042 毫秒         |
| **Entity Framework 6** | 647127040                 | 190228 毫秒          | 832798720                 | 195521 毫秒          |

实体框架 5 在运行结束时的内存占用量将比实体框架 6 少。 实体框架 6 使用的额外内存是启用新功能和更好性能的额外内存结构和代码的结果。

使用 ObjectStateManager 时，内存占用空间也有明显差异。 实体框架 5 在跟踪我们从数据库中实现的所有实体时，其占用空间增加了 30%。 实体框架 6 这样做时占用空间增加了 28%。

在时间方面，实体框架 6 在此测试中以较大优势优于实体框架 5。 实体框架 6 在实体框架 5 消耗的时间大约 16% 的时间内完成了测试。 此外，使用 ObjectStateManager 时，实体框架 5 需要 9% 的时间才能完成。 相比之下，实体框架 6 在使用 ObjectStateManager 时使用的时间要长 3%。

## <a name="6-query-execution-options"></a>6 查询执行选项

实体框架提供了几种不同的查询方法。 我们将介绍以下选项，比较每个选项的优缺点，并检查它们的性能特征：

-   林Q 到实体。
-   没有跟踪 LINQ 到实体。
-   对象查询的实体 SQL。
-   实体命令的实体 SQL。
-   执行存储查询。
-   SqlQuery。
-   编译查询。

### <a name="61-linq-to-entities-queries"></a>6.1 LINQ 到实体查询

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

**优点**

-   适用于 CUD 操作。
-   完全具体化的对象。
-   最简单的编写语法内置于编程语言中。
-   性能好。

**缺点**

-   某些技术限制，例如：
    -   对外部 JOIN 查询使用默认IfEmpty的模式会导致比实体 SQL 中简单的外部 JOIN 语句更复杂的查询。
    -   您仍然不能将 LIKE 用于常规模式匹配。

### <a name="62-no-tracking-linq-to-entities-queries"></a>6.2 没有跟踪 LINQ 到实体查询

当上下文派生对象上下文时：

``` csharp
context.Products.MergeOption = MergeOption.NoTracking;
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

当上下文派生 DbContext 时：

``` csharp
var q = context.Products.AsNoTracking()
                        .Where(p => p.Category.CategoryName == "Beverages");
```

**优点**

-   与常规 LINQ 查询性能提高。
-   完全具体化的对象。
-   最简单的编写语法内置于编程语言中。

**缺点**

-   不适合 CUD 操作。
-   某些技术限制，例如：
    -   对外部 JOIN 查询使用默认IfEmpty的模式会导致比实体 SQL 中简单的外部 JOIN 语句更复杂的查询。
    -   您仍然不能将 LIKE 用于常规模式匹配。

请注意，即使未指定 NoTracking，也不会跟踪项目标量属性的查询。 例如：

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages").Select(p => new { p.ProductName });
```

此特定查询未显式指定为 NoTracking，但由于它未具体化对象状态管理器已知的类型，因此不会跟踪具体化结果。

### <a name="63-entity-sql-over-an-objectquery"></a>6.3 对象查询的实体 SQL

``` csharp
ObjectQuery<Product> products = context.Products.Where("it.Category.CategoryName = 'Beverages'");
```

**优点**

-   适用于 CUD 操作。
-   完全具体化的对象。
-   支持查询计划缓存。

**缺点**

-   涉及文本查询字符串，与内置于语言中的查询构造相比，这些字符串更容易出现用户错误。

### <a name="64-entity-sql-over-an-entity-command"></a>6.4 实体命令的实体 SQL

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

**优点**

-   支持 .NET 4.0 中的查询计划缓存（.NET 4.5 中的所有其他查询类型都支持计划缓存）。

**缺点**

-   涉及文本查询字符串，与内置于语言中的查询构造相比，这些字符串更容易出现用户错误。
-   不适合 CUD 操作。
-   结果不会自动具体化，必须从数据读取器读取。

### <a name="65-sqlquery-and-executestorequery"></a>6.5 SqlQuery 和执行存储查询

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

ExecyteStore查询：

``` csharp
var beverages = context.ExecuteStoreQuery<Product>(
@"     SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued, P.DiscontinuedDate
       FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
       WHERE        (C.CategoryName = 'Beverages')"
);
```

**优点**

-   通常性能最快，因为计划编译器是绕过的。
-   完全具体化的对象。
-   适用于从 DbSet 使用的 CUD 操作。

**缺点**

-   查询是文本和容易出错的。
-   通过使用存储语义而不是概念语义，查询绑定到特定的后端。
-   存在继承时，手工制作的查询需要考虑所请求类型的映射条件。

### <a name="66-compiledquery"></a>6.6 编译查询

``` csharp
private static readonly Func<NorthwindEntities, string, IQueryable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
    (NorthwindEntities context, string categoryName) =>
        context.Products.Where(p => p.Category.CategoryName == categoryName)
        );
…
var q = context.InvokeProductsForCategoryCQ("Beverages");
```

**优点**

-   与常规 LINQ 查询（LINQ 查询）提供高达 7% 的性能改进。
-   完全具体化的对象。
-   适用于 CUD 操作。

**缺点**

-   增加了复杂性和编程开销。
-   在编译的查询上作曲时，性能改进将丢失。
-   某些 LINQ 查询不能编写为编译查询 - 例如，匿名类型的投影。

### <a name="67-performance-comparison-of-different-query-options"></a>6.7 不同查询选项的性能比较

在未进行时间创建时的简单微观基准测试已过。 我们在受控环境中对一组非缓存实体进行了 5000 次查询。 这些数字应该带有警告：它们并不反映应用程序产生的实际数字，而是非常准确地测量了在比较 Apple 到 apple 的不同查询选项时存在的性能差异量，不包括创建新上下文的成本。

| EF  | 测试                                 | 时间（毫秒） | 内存   |
|:----|:-------------------------------------|:----------|:---------|
| EF5 | 对象上下文 ESQL                   | 2414      | 38801408 |
| EF5 | 对象上下文林克查询             | 2692      | 38277120 |
| EF5 | 数据库上下文林克查询无跟踪     | 2818      | 41840640 |
| EF5 | 数据库上下文林克查询                 | 2930      | 41771008 |
| EF5 | 对象上下文林q查询无跟踪 | 3013      | 38412288 |
|     |                                      |           |          |
| EF6 | 对象上下文 ESQL                   | 2059      | 46039040 |
| EF6 | 对象上下文林克查询             | 3074      | 45248512 |
| EF6 | 数据库上下文林克查询无跟踪     | 3125      | 47575040 |
| EF6 | 数据库上下文林克查询                 | 3420      | 47652864 |
| EF6 | 对象上下文林q查询无跟踪 | 3593      | 45260800 |

![EF5 微型基准，5000 个暖迭代](~/ef6/media/ef5micro5000warm.png)

![EF6 微型基准，5000 个暖迭代](~/ef6/media/ef6micro5000warm.png)

微基准对代码中的小更改非常敏感。 在这种情况下，实体框架 5 和实体框架 6 的成本之间的差额是由于[增加了拦截](~/ef6/fundamentals/logging-and-interception.md)和[事务性改进](~/ef6/saving/transactions.md)。 然而，这些微观基准数字是实体框架所做操作的一个很小的碎片中放大的视野。 从实体框架 5 升级到实体框架 6 时，暖查询的实际方案不应看到性能回归。

为了比较不同查询选项的实际性能，我们创建了 5 个单独的测试变体，其中我们使用不同的查询选项来选择类别名称为"饮料"的所有产品。 每个迭代包括创建上下文的成本，以及实现所有返回的实体的成本。 在采用 1000 个有时算迭代的总和之前，将不及时运行 10 个迭代。 显示的结果是从每次测试的 5 次运行中获取的中位运行。 有关详细信息，请参阅附录 B，其中包含测试的代码。

| EF  | 测试                                        | 时间（毫秒） | 内存   |
|:----|:--------------------------------------------|:----------|:---------|
| EF5 | 对象上下文实体命令                | 621       | 39350272 |
| EF5 | 数据库上的 DbContext Sql 查询             | 825       | 37519360 |
| EF5 | 对象上下文存储查询                   | 878       | 39460864 |
| EF5 | 对象上下文林q查询无跟踪        | 969       | 38293504 |
| EF5 | 使用对象查询的对象上下文实体 Sql | 1089      | 38981632 |
| EF5 | 对象上下文编译查询                | 1099      | 38682624 |
| EF5 | 对象上下文林克查询                    | 1152      | 38178816 |
| EF5 | 数据库上下文林克查询无跟踪            | 1208      | 41803776 |
| EF5 | DbSet 上的数据库上下文 Sql 查询                | 1414      | 37982208 |
| EF5 | 数据库上下文林克查询                        | 1574      | 41738240 |
|     |                                             |           |          |
| EF6 | 对象上下文实体命令                | 480       | 47247360 |
| EF6 | 对象上下文存储查询                   | 493       | 46739456 |
| EF6 | 数据库上的 DbContext Sql 查询             | 614       | 41607168 |
| EF6 | 对象上下文林q查询无跟踪        | 684       | 46333952 |
| EF6 | 使用对象查询的对象上下文实体 Sql | 767       | 48865280 |
| EF6 | 对象上下文编译查询                | 788       | 48467968 |
| EF6 | 数据库上下文林克查询无跟踪            | 878       | 47554560 |
| EF6 | 对象上下文林克查询                    | 953       | 47632384 |
| EF6 | DbSet 上的数据库上下文 Sql 查询                | 1023      | 41992192 |
| EF6 | 数据库上下文林克查询                        | 1290      | 47529984 |


![EF5 暖查询 1000 次迭代](~/ef6/media/ef5warmquery1000.png)

![EF6 暖查询 1000 次迭代](~/ef6/media/ef6warmquery1000.png)

> [!NOTE]
> 为完整，我们包括一个变体，其中我们在实体命令上执行实体 SQL 查询。 但是，由于此类查询的结果未具体化，因此比较不一定是苹果到苹果。 测试包括接近具体化，试图使比较更公平。

在此端到端案例中，实体框架 6 优于实体框架 5，因为对堆栈的几个部分进行了性能改进，包括更轻的 DbContext 初始化和更快的元数据收集&lt;T&gt;查找。

## <a name="7-design-time-performance-considerations"></a>7 设计时间性能注意事项

### <a name="71-inheritance-strategies"></a>7.1 继承策略

使用实体框架时的另一个性能考虑是您使用的继承策略。 实体框架支持 3 种基本类型的继承及其组合：

-   每个层次结构的表 （TPH） - 其中每个继承集映射到具有区分列的表，以指示在行中表示层次结构中的特定类型。
-   每个类型 （TPT） 的表 - 其中每种类型在数据库中都有自己的表;子表仅定义父表不包含的列。
-   每个类的表 （TPC） - 其中每种类型的在数据库中都有自己的完整表;子表定义其所有字段，包括父类型中定义的字段。

如果模型使用 TPT 继承，则生成的查询将比其他继承策略生成的查询复杂，这可能导致存储上的执行时间较长。通过 TPT 模型生成查询并实现结果对象通常需要更长时间。

请参阅"在实体框架中使用 TPT（按类型表）继承时的性能注意事项"MSDN 博客文章： \< https://docs.microsoft.com/archive/blogs/adonet/performance-considerations-when-using-tpt-table-per-type-inheritance-in-the-entity-framework>。

#### <a name="711-avoiding-tpt-in-model-first-or-code-first-applications"></a>7.1.1 避免在模型优先或代码优先应用程序中使用 TPT

当您在具有 TPT 架构的现有数据库上创建模型时，您没有很多选项。 但是，在使用 Model First 或代码优先创建应用程序时，出于性能问题，应避免 TPT 继承。

在实体设计器向导中使用模型优先时，您将获得模型中任何继承的 TPT。 如果要使用 Model First 切换到 TPH 继承策略，可以使用 Visual Studio 库 （中的\<http://visualstudiogallery.msdn.microsoft.com/df3541c3-d833-4b65-b942-989e7ec74c87/>)实体设计器数据库生成电源包）中的"实体设计器数据库生成电源包"。

当使用 Code First 配置具有继承的模型映射时，EF 默认将使用 TPH，因此继承层次结构中的所有实体都将映射到同一表。 有关详细信息，请参阅 MSDN 杂志 （）[http://msdn.microsoft.com/magazine/hh126815.aspx](https://msdn.microsoft.com/magazine/hh126815.aspx)中"实体框架4.1中代码优先"一文中的"使用流畅 API 映射"部分。

### <a name="72-upgrading-from-ef4-to-improve-model-generation-time"></a>7.2 从 EF4 升级以提高模型生成时间

生成模型存储层 （SSDL） 的算法的 SQL Server 特定改进可在实体框架 5 和 6 中提供，并在安装 Visual Studio 2010 SP1 时作为实体框架 4 的更新提供。 以下测试结果演示了生成非常大的模型时的改进，在这种情况下，Navision 模型。 有关附录 C 的更多详细信息，请参阅附录 C。

该模型包含 1005 个实体集和 4227 个关联集。

| 配置                              | 所消耗的时间细目                                                                                                                                               |
|:-------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 视觉工作室 2010， 实体框架 4     | SSDL 生成： 2 小时 27 分钟 <br/> 映射生成：1 秒 <br/> CSDL 生成：1 秒 <br/> 对象层生成：1 秒 <br/> 视图生成： 2 小时 14 分钟 |
| 可视化工作室 2010 SP1， 实体框架 4 | SSDL 生成：1 秒 <br/> 映射生成：1 秒 <br/> CSDL 生成：1 秒 <br/> 对象层生成：1 秒 <br/> 视图生成： 1 小时 53 分钟   |
| 视觉工作室 2013， 实体框架 5     | SSDL 生成：1 秒 <br/> 映射生成：1 秒 <br/> CSDL 生成：1 秒 <br/> 对象层生成：1 秒 <br/> 视图生成： 65 分钟    |
| 视觉工作室 2013， 实体框架 6     | SSDL 生成：1 秒 <br/> 映射生成：1 秒 <br/> CSDL 生成：1 秒 <br/> 对象层生成：1 秒 <br/> 视图生成：28 秒。   |


值得注意的是，在生成 SSDL 时，负载几乎完全花费在 SQL Server 上，而客户端开发计算机正在等待从服务器返回的结果。 DBA 应特别赞赏这一改进。 还值得注意的是，模型生成的全部成本基本上都发生在视图生成中。

### <a name="73-splitting-large-models-with-database-first-and-model-first"></a>7.3 使用数据库优先和模型第一拆分大型模型

随着模型尺寸的增加，设计器表面变得杂乱无章且难以使用。 我们通常认为具有 300 多个实体的模型太大，无法有效地使用设计器。 以下博客文章描述了拆分大型模型的几个选项： \< https://docs.microsoft.com/archive/blogs/adonet/working-with-large-models-in-entity-framework-part-2>。

该帖子是为实体框架的第一个版本编写的，但步骤仍然适用。

### <a name="74-performance-considerations-with-the-entity-data-source-control"></a>7.4 实体数据源控制的性能注意事项

我们已经看到多线程性能和压力测试中的情况，其中使用 EntityDataSource 控件的 Web 应用程序的性能显著恶化。 根本原因是 EntityDataSource 反复调用 Web 应用程序引用的程序集上的元数据工作区.LoadFromAssembly，以发现要用作实体的类型。

解决方案是将实体数据源的上下文类型名称设置为派生对象上下文类的类型名称。 这将关闭扫描所有引用的程序集的实体类型的机制。

设置 ContextTypeName 字段还可以防止功能问题，即 .NET 4.0 中的实体数据源在无法通过反射从程序集加载类型时引发反射类型加载异常。 此问题已在 .NET 4.5 中修复。

### <a name="75-poco-entities-and-change-tracking-proxies"></a>7.5 POCO 实体和更改跟踪代理

实体框架使您能够将自定义数据类与数据模型一起使用，而无需对数据类本身进行任何修改。 这意味着可以将“纯旧式”CLR 对象 (POCO)（例如，现有的域对象）与数据模型一起使用。 这些 POCO 数据类（也称为持久性无知对象）映射到数据模型中定义的实体，支持大多数相同的查询，插入、更新和删除实体数据模型工具生成的实体类型的行为。

实体框架还可以创建从 POCO 类型派生的代理类，当您希望启用诸如 POCO 实体上的延迟加载和自动更改跟踪等功能时，将使用这些类。 您的 POCO 类必须满足某些要求，才能允许实体框架使用代理，如下所述： [http://msdn.microsoft.com/library/dd468057.aspx](https://msdn.microsoft.com/library/dd468057.aspx)。

每次实体的任何属性发生更改时，机会跟踪代理都会通知对象状态管理器，因此实体框架会一直了解实体的实际状态。 这是通过将通知事件添加到属性的 setter 方法的正文，以及让对象状态管理器处理此类事件来实现的。 请注意，由于实体框架创建了一组附加的事件，创建代理实体通常比创建非代理 POCO 实体的成本更高。

当 POCO 实体没有更改跟踪代理时，可以通过将实体的内容与以前保存状态的副本进行比较来找到更改。 当您的上下文中有许多实体时，或者当实体具有非常大的属性时，即使自上次比较以来，这些属性均未更改，这种深层比较将成为一个漫长的过程。

总之：创建更改跟踪代理时，您将支付性能影响，但当实体具有许多属性或模型中有许多实体时，更改跟踪将帮助您加快更改检测过程。 对于具有少量属性的实体，其中实体数量不会增长太多，因此具有更改跟踪代理可能没有多大好处。

## <a name="8-loading-related-entities"></a>8 加载相关实体

### <a name="81-lazy-loading-vs-eager-loading"></a>8.1 延迟加载与热负荷

实体框架提供了几种加载与目标实体相关的实体的不同方法。 例如，当您查询产品时，相关订单将加载到对象状态管理器中的方式不同。 从性能角度来看，加载相关实体时要考虑的最大问题是是使用延迟加载还是"渴望加载"。

使用"热加载"时，相关实体将随目标实体集一起加载。 在查询中使用"包含"语句来指示要引入哪些相关实体。

使用延迟加载时，初始查询仅引入目标实体集。 但是，每当访问导航属性时，都会针对存储发出另一个查询以加载相关实体。

加载实体后，实体的任何进一步查询都将直接从对象状态管理器加载它，无论您是使用延迟加载还是热要加载。

### <a name="82-how-to-choose-between-lazy-loading-and-eager-loading"></a>8.2 如何在延迟加载和渴望加载之间进行选择

重要的是，您了解延迟加载和热加载之间的区别，以便您可以为您的应用程序做出正确的选择。 这将有助于评估针对数据库的多个请求与可能包含大型负载的单个请求之间的权衡。 在应用程序的某些部分使用热加载和其他部分的延迟加载可能是合适的。

作为引擎盖下所发生的事情的一个示例，假设您要查询居住在英国的客户及其订单数量。

**使用热负荷**

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

使用热要加载时，您将发出一个查询，返回所有客户和所有订单。 存储命令如下所示：

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

使用延迟加载时，最初将发出以下查询：

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

每次访问客户的"订单"导航属性时，都会针对商店发出另一个查询，如下所示：

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

#### <a name="821-lazy-loading-versus-eager-loading-cheat-sheet"></a>8.2.1 延迟加载与渴望加载备忘单

选择热切装载和延迟装载，没有一刀切的东西。 首先尝试了解两种策略之间的差异，以便您可以做出明智的决策;此外，请考虑您的代码是否适合以下任何方案：

| 场景                                                                    | 我们的建议                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|:----------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 是否需要从提取的实体访问许多导航属性？ | **否**- 两个选项都可能都能做到。 但是，如果查询带来的有效负载不是太大，则使用 Eager 加载可能会获得性能优势，因为它需要较少的网络往返行程来实现对象。 <br/> <br/> **是**- 如果需要从实体访问许多导航属性，则可以通过在查询中使用多个包含语句来使用"渴望加载"来执行此操作。 包含的实体越多，查询返回的有效负载就越大。 将三个或多个实体包含在查询中后，请考虑切换到延迟加载。 |
| 您确切知道运行时需要哪些数据吗？                   | **否**- 延迟加载将更好地为您。 否则，您最终可能会查询不需要的数据。 <br/> <br/> **是的**- 渴望加载可能是您最好的选择;这将有助于更快地加载整个集。 如果查询需要获取大量数据，并且速度太慢，请尝试"延迟加载"。                                                                                                                                                                                                                                                       |
| 您的代码执行是否远离数据库？ （增加网络延迟）  | **否**- 当网络延迟不是问题时，使用延迟加载可能会简化代码。 请记住，应用程序的拓扑可能会更改，因此不要认为数据库邻近性是理所当然的。 <br/> <br/> **是**- 当网络出现问题时，只有您才能决定哪种方案更适合您的方案。 通常，热负荷会更好，因为它需要更少的往返行程。                                                                                                                                                                                                      |


#### <a name="822-performance-concerns-with-multiple-includes"></a>8.2.2 具有多个包括的性能问题

当我们听到涉及服务器响应时间问题的性能问题时，问题的根源是经常使用多个 Include 语句进行查询。 虽然在查询中包含相关实体很强大，但了解封面下发生的情况非常重要。

包含多个 Include 语句的查询需要相当长的时间才能通过内部计划编译器来生成存储命令。 这一大部分时间都花在尝试优化生成的查询上。 生成的存储命令将包含每个"包含"的外部联接或联合，具体取决于您的映射。 像这样的查询将在单个负载中从数据库引入大型连接图形，从而消除任何带宽问题，尤其是在有效负载中存在大量冗余时（例如，当多个级别的 Include 用于在一对多方向上遍历关联时）。

通过使用 ToTraceString 并在 SQL 服务器管理工作室中执行存储命令以查看有效负载大小，可以检查查询返回过大的有效负载的情况。 在这种情况下，您可以尝试减少查询中的包含语句数，以便仅引入所需的数据。 或者，您可能能够将查询分解为较小的子查询序列，例如：

**在断开查询之前：**

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

这仅适用于跟踪的查询，因为我们正在利用上下文自动执行标识解析和关联修复的能力。

与延迟加载一样，权衡将是对较小负载的更多查询。 您还可以使用单个属性的预测来仅显式选择每个实体所需的数据，但在这种情况下，您将不会加载实体，并且不支持更新。

#### <a name="823-workaround-to-get-lazy-loading-of-properties"></a>8.2.3 获取延迟加载属性的解决方法

实体框架当前不支持延迟加载标量或复杂属性。 但是，如果表包含大型对象（如 BLOB），则可以使用表拆分将大型属性分隔为单独的实体。 例如，假设您有一个包含 varbinary 照片列的产品表。 如果查询中频繁不需要访问此属性，则可以使用表拆分仅引入通常需要的实体部分。 仅当您明确需要产品照片时，才会加载表示产品照片的实体。

吉尔·芬克的"实体框架中的表拆分"博客文章是演示如何启用表拆分的一个很好的资源： \< http://blogs.microsoft.co.il/blogs/gilf/archive/2009/10/13/table-splitting-in-entity-framework.aspx>.

## <a name="9-other-considerations"></a>9 其他注意事项

### <a name="91-server-garbage-collection"></a>9.1 服务器垃圾回收

某些用户可能会遇到资源争用，从而限制在垃圾回收器未正确配置时期望的并行性。 每当在多线程方案中使用 EF 时，或任何类似于服务器端系统的应用程序中，请确保启用服务器垃圾回收。 这是通过应用程序配置文件中的简单设置完成的：

``` xml
<?xmlversion="1.0" encoding="utf-8" ?>
<configuration>
        <runtime>
               <gcServer enabled="true" />
        </runtime>
</configuration>
```

这将减少线程争用，并在 CPU 饱和方案中将吞吐量提高高达 30%。 通常，您应该始终使用经典垃圾回收（更好地针对 UI 和客户端方案）以及服务器垃圾回收测试应用程序的表现。

### <a name="92-autodetectchanges"></a>9.2 自动检测更改

如前所述，当对象缓存具有多个实体时，实体框架可能会显示性能问题。 某些操作（如添加、删除、查找、输入和保存更改）会触发对检测更改的调用，这些调用可能会根据对象缓存变得多大而消耗大量 CPU。 原因是对象缓存和对象状态管理器尝试在对上下文执行的每个操作上保持尽可能同步，以便在各种方案下保证生成的数据正确。

通常，最好在应用程序的整个生命周期内启用实体框架的自动更改检测。 如果您的方案受到 CPU 使用率高的负面影响，并且您的配置文件指示罪魁祸首是调用检测更改，请考虑暂时关闭代码敏感部分中的自动检测更改：

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

在关闭自动检测更改之前，最好了解这可能会导致实体框架失去跟踪有关实体上发生的更改的某些信息的能力。 如果处理不正确，则可能会导致应用程序上的数据不一致。 有关关闭自动检测更改的详细信息，请阅读\<http://blog.oneunicorn.com/2012/03/12/secrets-of-detectchanges-part-3-switching-off-automatic-detectchanges/>。

### <a name="93-context-per-request"></a>9.3 每个请求的上下文

实体框架的上下文旨在用作短期实例，以便提供最佳的性能体验。 上下文预期是短暂的和丢弃的，因此已经实现为非常轻量级，并尽可能重用元数据。 在 Web 方案中，请务必牢记这一点，并且不应具有超过单个请求持续时间的上下文。 同样，在非 Web 方案中，应根据您对实体框架中不同缓存级别的理解而丢弃上下文。 一般来说，在应用程序的整个生命周期中，应避免使用上下文实例，以及每个线程和静态上下文的上下文。

### <a name="94-database-null-semantics"></a>9.4 数据库无效语义

默认情况下，实体框架将生成具有 C\#空比较语义的 SQL 代码。 请参考以下示例查询：

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

在此示例中，我们将许多可空变量与实体上的空属性（如供应商 ID 和 UnitPrice）进行比较。 此查询生成的 SQL 将询问参数值是否与列值相同，或者参数和列值是否为空。 这将隐藏数据库服务器处理空的方式，并将跨不同的数据库供应商提供一致的\#C null 体验。 另一方面，生成的代码有点复杂，当查询语句中的比较量增长到大量时，可能无法很好地执行。

处理这种情况的一种方法是使用数据库空语义。 请注意，这可能与 C\# null 语义的行为不同，因为现在实体框架将生成更简单的 SQL，公开数据库引擎处理空值的方式。 数据库空语义可以针对上下文配置使用单个配置行激活每个上下文：

``` csharp
                context.Configuration.UseDatabaseNullSemantics = true;
```

使用数据库空语义时，中小型查询不会显示可察觉的性能改进，但在具有大量潜在空比较的查询上，差异将变得更加明显。

在上面的示例查询中，在受控环境中运行的微基准中，性能差异小于 2%。

### <a name="95-async"></a>9.5 异步

实体框架 6 在 .NET 4.5 或更高版本上运行时引入了对异步操作的支持。 在大多数情况下，具有 IO 相关争用的应用程序将从使用异步查询和保存操作中受益最大。 如果应用程序没有受到 IO 争用的影响，则在最好情况下，使用异步将同步运行，并在与同步调用相同的时间内返回结果，或者在最坏的情况下，只需将执行推迟到异步任务，并添加额外的时间来完成您的方案。

有关异步编程的工作原理的信息，这将有助于您确定异步程序是否会提高应用程序访问[http://msdn.microsoft.com/library/hh191443.aspx](https://msdn.microsoft.com/library/hh191443.aspx)的性能。 有关在实体框架上使用异步操作的详细信息，请参阅[异步查询和保存](~/ef6/fundamentals/async.md
)。

### <a name="96-ngen"></a>9.6 NGEN

实体框架 6 不在 .NET 框架的默认安装中。 因此，默认情况下，实体框架程序集不是 NGEN d，这意味着所有实体框架代码都受与任何其他 MSIL 程序集相同的 JIT 成本的约束。 这可能会降低 F5 在开发时的经验，也会降低应用程序在生产环境中的冷启动。 为了降低 JIT 的 CPU 和内存成本，建议酌情使用实体框架映像进行 NGEN。 有关如何使用 NGEN 提高实体框架 6 的启动性能的详细信息，请参阅[使用 NGen 提高启动性能](~/ef6/fundamentals/performance/ngen.md)。

### <a name="97-code-first-versus-edmx"></a>9.7 代码优先与 EDMX

实体框架通过具有概念模型（对象）、存储架构（数据库）和两者之间映射的内存中表示形式，导致面向对象编程和关系数据库之间的阻抗不匹配问题。 此元数据称为实体数据模型，简称 EDM。 从这个 EDM 中，实体框架将从内存中的对象派生到往返数据的视图到数据库并返回。

当实体框架与正式指定概念模型、存储架构和映射的 EDMX 文件一起使用时，模型加载阶段只需验证 EDM 是否正确（例如，确保没有缺少映射），然后生成视图，然后验证视图并准备好使用此元数据。 只有这样，才能执行查询或将新数据保存到数据存储。

代码优先方法的核心是一个复杂的实体数据模型生成器。 实体框架必须从提供的代码生成 EDM;它通过分析模型中涉及的类、应用约定并通过 Fluent API 配置模型来达到这一要求。 构建 EDM 后，实体框架的工作方式与项目中存在 EDMX 文件的方式相同。 因此，从 Code First 构建模型会增加额外的复杂性，与使用 EDMX 相比，实体框架的启动时间会变慢。 成本完全取决于正在构建的模型的大小和复杂性。

在选择使用 EDMX 与代码优先时，请务必了解代码 First 引入的灵活性会增加首次构建模型的成本。 如果应用程序能够承受此首次加载的成本，那么通常 Code First 将是首选方式。

## <a name="10-investigating-performance"></a>10 调查性能

### <a name="101-using-the-visual-studio-profiler"></a>10.1 使用可视化工作室探查器

如果实体框架存在性能问题，则可以使用 Visual Studio 中内置的探查器来查看应用程序花费的时间。 这是我们用于在"探索ADO.NET实体框架的性能 - 第 1 部分"博客文章（\<https://docs.microsoft.com/archive/blogs/adonet/exploring-the-performance-of-the-ado-net-entity-framework-part-1>)显示实体框架在冷热查询期间花费时间）中生成饼图的工具。

数据和建模客户咨询团队编写的"使用 Visual Studio 2010 探查器分析实体框架"博客文章显示了他们如何使用探查器调查性能问题的真实示例。\<https://docs.microsoft.com/archive/blogs/dmcat/profiling-entity-framework-using-the-visual-studio-2010-profiler>. 这篇文章是为窗口应用程序编写的。 如果需要分析 Web 应用程序，Windows 性能记录仪 （WPR） 和 Windows 性能分析器 （WPA） 工具可能比在 Visual Studio 工作更好。 WPR 和 WPA 是 Windows 性能工具包的一部分，该工具包包含在 Windows[http://www.microsoft.com/download/details.aspx?id=39982](https://www.microsoft.com/download/details.aspx?id=39982)评估和部署工具包 （） 中。

### <a name="102-applicationdatabase-profiling"></a>10.2 应用程序/数据库分析

像可视化工作室中内置的探查器这样的工具告诉您应用程序将花时间的位置。另一种类型的探查器可用于根据需求在生产或预生产中对正在运行的应用程序进行动态分析，并查找常见的陷阱和数据库访问的抗模式。

两个市售的探查器是实体框架探查\<http://efprof.com>)器（和 ORMProfiler（。 \< http://ormprofiler.com>)

如果您的应用程序是使用代码优先的 MVC 应用程序，则可以使用 StackExchange 的 Mini Profiler。 斯科特·汉塞尔曼在他的博客中描述了这个工具： \< http://www.hanselman.com/blog/NuGetPackageOfTheWeek9ASPNETMiniProfilerFromStackExchangeRocksYourWorld.aspx>.

有关分析应用程序的数据库活动的详细信息，请参阅 Julie Lerman 的 MSDN 杂志文章，标题为[实体框架中的分析数据库活动](https://msdn.microsoft.com/magazine/gg490349.aspx)。

### <a name="103-database-logger"></a>10.3 数据库记录器

如果使用实体框架 6，还考虑使用内置日志记录功能。 可以指示上下文的数据库属性通过简单的单行配置记录其活动：

``` csharp
    using (var context = newQueryComparison.DbC.NorthwindEntities())
    {
        context.Database.Log = Console.WriteLine;
        var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
        q.ToList();
    }
```

在此示例中，数据库活动将记录到控制台，但可以将 Log 属性配置为调用任何操作&lt;字符串&gt;委托。

如果要在不重新编译的情况下启用数据库日志记录，并且正在使用实体框架 6.1 或更高版本，则可以通过在应用程序的 Web.config 或 app.config 文件中添加拦截器来执行此操作。

``` xml
  <interceptors>
    <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
      <parameters>
        <parameter value="C:\Path\To\My\LogOutput.txt"/>
      </parameters>
    </interceptor>
  </interceptors>
```

有关如何在不重新编译的情况下添加日志记录的详细信息，\<http://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/>请访问 。

## <a name="11-appendix"></a>11 附录

### <a name="111-a-test-environment"></a>11.1 A. 测试环境

此环境使用 2 台计算机设置，数据库与客户端应用程序分开的计算机上。 计算机位于同一机架中，因此网络延迟相对较低，但比单机环境更逼真。

#### <a name="1111-app-server"></a>11.1.1 应用服务器

##### <a name="11111-software-environment"></a>11.1.1.1 软件环境

-   实体框架 4 软件环境
    -   操作系统名称：Windows 服务器 2008 R2 企业 SP1。
    -   视觉工作室 2010 – 终极。
    -   可视化工作室 2010 SP1 （仅适用于某些比较）。
-   实体框架 5 和 6 软件环境
    -   操作系统名称：Windows 8.1 企业版
    -   视觉工作室 2013 – 终极。

##### <a name="11112-hardware-environment"></a>11.1.1.2 硬件环境

-   双处理器：英特尔（R） Xeon（R） CPU L5520 W3530 = 2.27GHz，2261 Mhz8 GHz，4 核（s），84 逻辑处理器。
-   2412 GB RamRAM。
-   136 GB SCSI250GB SATA 7200 rpm 3GB/s 驱动器拆分为 4 个分区。

#### <a name="1112-db-server"></a>11.1.2 数据库服务器

##### <a name="11121-software-environment"></a>11.1.2.1 软件环境

-   操作系统名称：Windows 服务器 2008 R28.1 企业 SP1。
-   SQL 服务器 2008 R22012。

##### <a name="11122-hardware-environment"></a>11.1.2.2 硬件环境

-   单处理器：英特尔（R） Xeon（R） CPU L5520 = 2.27GHz，2261 MhzES-1620 0 = 3.60GHz，4 个核心（s），8 个逻辑处理器。
-   824 GB 拉姆拉姆。
-   465 GB ATA500GB SATA 7200 rpm 6GB/s 驱动器拆分为 4 个分区。

### <a name="112-b-query-performance-comparison-tests"></a>11.2 B. 查询性能比较测试

北风模型用于执行这些测试。 它是使用实体框架设计器从数据库生成的。 然后，使用以下代码比较查询执行选项的性能：

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

### <a name="113-c-navision-model"></a>11.3 C. 纳维视觉模型

Navision 数据库是一个大型数据库，用于演示 Microsoft 动态和导航。 生成的概念模型包含 1005 个实体集和 4227 个关联集。 测试中使用的模型为"平面" - 未向其添加继承。

#### <a name="1131-queries-used-for-navision-tests"></a>11.3.1 用于 Navision 测试的查询

与 Navision 模型一起使用的查询列表包含 3 类实体 SQL 查询：

##### <a name="11311-lookup"></a>11.3.1.1 查找

没有聚合的简单查找查询

-   数量： 16232
-   示例：

``` xml
  <Query complexity="Lookup">
    <CommandText>Select value distinct top(4) e.Idle_Time From NavisionFKContext.Session as e</CommandText>
  </Query>
```

##### <a name="11312singleaggregating"></a>11.3.1.2 单聚合

具有多个聚合但没有子值（单个查询）的普通 BI 查询

-   数量： 2313
-   示例：

``` xml
  <Query complexity="SingleAggregating">
    <CommandText>NavisionFK.MDF_SessionLogin_Time_Max()</CommandText>
  </Query>
```

其中 MDF\_\_会话\_登录时间最大值（） 在模型中定义为：

``` xml
  <Function Name="MDF_SessionLogin_Time_Max" ReturnType="Collection(DateTime)">
    <DefiningExpression>SELECT VALUE Edm.Min(E.Login_Time) FROM NavisionFKContext.Session as E</DefiningExpression>
  </Function>
```

##### <a name="11313aggregatingsubtotals"></a>11.3.1.3 聚合子总计

包含聚合和小计的 BI 查询（通过全部联合）

-   计数： 178
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
