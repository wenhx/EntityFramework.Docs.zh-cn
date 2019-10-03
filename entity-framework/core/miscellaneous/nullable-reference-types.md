---
title: 使用可以为 null 的引用类型-EF Core
author: roji
ms.date: 09/09/2019
ms.assetid: bde4e0ee-fba3-4813-a849-27049323d301
uid: core/miscellaneous/nullable-reference-types
ms.openlocfilehash: ab35e63a6eeb2f02ed07a715fd65855b4d30eaf5
ms.sourcegitcommit: 6c28926a1e35e392b198a8729fc13c1c1968a27b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71813455"
---
# <a name="working-with-nullable-reference-types"></a>使用可以为 Null 的引用类型

C#8引入了一种名[为 null 的引用类型](/dotnet/csharp/tutorials/nullable-reference-types)的新功能，允许对引用类型进行批注，以指示它是否可用于包含 null。 如果你不熟悉此功能，则建议你通过阅读C#文档来使自己熟悉该功能。

本页介绍 EF Core 支持可以为 null 的引用类型，并介绍了使用它们的最佳做法。

## <a name="required-and-optional-properties"></a>必需属性和可选属性

对于必需属性和可选属性及其与可为 null 的引用类型的交互，主要文档是[必需的和可选的属性](xref:core/modeling/required-optional)页。 建议首先阅读该页面。

> [!NOTE]
> 在现有项目上启用可以为 null 的引用类型时要格外小心：现在配置为可选的引用类型属性现在将配置为 "必需"，除非它们显式批注为可为 null。 管理关系数据库架构时，这可能会导致生成更改数据库列的为 null 性的迁移。

## <a name="dbcontext-and-dbset"></a>DbContext 和 DbSet

如果启用了可以为 null 的引用C#类型，则编译器将为任何未初始化的不可为 null 的属性发出警告，因为这将包含 null。 因此，在上下文中定义不可为 null `DbSet`的常见做法现在会生成一个警告。 但是，EF Core 始终初始化 DbContext `DbSet`派生类型上的所有属性，因此即使编译器不知道此操作，也不能保证它们永远不会为 null。 因此，建议保留你`DbSet`的属性不能为 null-允许你在不使用 null 检查的情况下访问它们，并通过使用包容性运算符（！）的帮助将其显式设置为 null 来使编译器警告静音：

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/NullableReferenceTypesContext.cs?name=Context&highlight=3-4)]

## <a name="non-nullable-properties-and-initialization"></a>不可以为 null 的属性和初始化

对于实体类型上的常规属性，未初始化的不可以为 null 的引用类型的编译器警告也是一个问题。 在上面的示例中，我们使用[构造函数绑定](xref:core/modeling/constructors)避免了这些警告，这是一项完美使用不可为 null 的属性的功能，确保它们始终初始化。 但是，在某些情况下，构造函数绑定不是一个选项：例如，不能以这种方式初始化导航属性。

所需的导航属性会带来额外的难度：尽管某个给定主体的依赖项始终存在，但特定查询可能会或不加载该依赖项，具体取决于程序中该点的需求（[请参阅不同模式正在加载数据](xref:core/querying/related-data)）。 同时，不需要将这些属性设置为可以为 null，因为这会强制对它们的所有访问权限，以检查是否有 null，即使它们是必需的。

处理这些方案的一种方法是使用一个可以为 null 的[支持字段](xref:core/modeling/backing-field)的不可为 null 的属性：

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Order.cs?range=12-17)]

由于导航属性不可为 null，因此配置了必需的导航;只要正确加载导航，就可以通过属性访问依赖项。 但是，如果在未事先正确加载相关实体的情况下访问属性，则会引发 InvalidOperationException，因为 API 协定的使用不正确。

作为 terser 的替代方法，可以使用包容性运算符（！）的帮助简单地将属性初始化为 null：

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Order.cs?range=19)]

实际的 null 值永远不会被视为除了编程错误之外的情况，例如，访问导航属性时，无需事先正确加载相关实体。

> [!NOTE]
> 包含对多个相关实体的引用的集合导航应始终不可为 null。 空集合意味着不存在相关实体，但列表本身不应为 null。

## <a name="navigating-and-including-nullable-relationships"></a>导航和包括可以为 null 的关系

当处理可选关系时，可能会遇到编译器警告，但不可能出现实际的 null 引用异常。 在转换和执行 LINQ 查询时，EF Core 确保在一个可选的相关实体不存在的情况下，将忽略对该实体的任何导航，而不是引发。 但是，编译器不知道这 EF Core 确保，并生成警告，就好像 LINQ 查询是在内存中执行的，而 LINQ to Objects。 因此，需要使用包容性运算符（！）来通知编译器无法实现实际的 null 值：

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Program.cs?range=46)]

在可选导航中包含多个级别的关系时，会发生类似的问题：

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Program.cs?range=36-39&highlight=2)]

如果你发现自己执行了很多操作，并且所涉及的实体类型是在 EF Core 查询中主要（或独占）使用的，请考虑使导航属性不可为 null，并将其配置为可通过熟知 API 或数据批注进行选择。 这将删除所有编译器警告，同时保持关系为可选;但是，如果你的实体是在 EF Core 之外遍历的，则你可能会观察到 null 值，尽管这些属性已批注为不可为 null。

## <a name="scaffolding"></a>基架

反向工程当前不支持[8 可为 Null 的C#引用类型功能](/dotnet/csharp/tutorials/nullable-reference-types)：EF Core 始终会C#生成假定功能已关闭的代码。 例如，可以将可为 null 的文本列基架为类型`string`为的属性，而不`string?`是用于配置是否需要属性的熟知 API 或数据批注。 您可以编辑基架代码并将其替换为C#可为 null 的批注。 [#15520](https://github.com/aspnet/EntityFrameworkCore/issues/15520)的问题跟踪了可为 null 的引用类型的基架支持。
