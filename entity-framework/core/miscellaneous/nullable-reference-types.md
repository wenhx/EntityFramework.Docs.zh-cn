---
title: 使用可以为 null 的引用类型-EF Core
description: '使用时使用 c # 可为 null 的引用类型 Entity Framework Core'
author: roji
ms.date: 09/09/2019
uid: core/miscellaneous/nullable-reference-types
ms.openlocfilehash: 648b79576838d2ba424b5216d5ad6811912f8ccb
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429710"
---
# <a name="working-with-nullable-reference-types"></a>使用可以为 Null 的引用类型

C# 8 引入了一项名 [为 null 的引用类型 (NRT) ](/dotnet/csharp/tutorials/nullable-reference-types)的新功能，允许对引用类型进行批注，以指示它是否可包含 null。 如果你不熟悉此功能，则建议你通过阅读 C# 文档来使自己熟悉该功能。

此页介绍 EF Core 对可为 null 的引用类型的支持，并介绍了使用它们的最佳做法。

## <a name="required-and-optional-properties"></a>必需属性和可选属性

对于必需属性和可选属性及其与可为 null 的引用类型的交互，主要文档是 [必需的和可选的属性](xref:core/modeling/entity-properties#required-and-optional-properties) 页。 建议首先阅读该页面。

> [!NOTE]
> 在现有项目上启用可以为 null 的引用类型时要格外小心：配置为可选的引用类型属性现在将配置为 "必需"，除非它们显式批注为可为 null。 管理关系数据库架构时，这可能会导致生成数据库列为空性更改的迁移。

## <a name="non-nullable-properties-and-initialization"></a>不可为空的属性和初始化

如果启用了可为空的引用类型，则 C# 编译器将为任何未初始化的不可为空的属性发出警告，因为这些属性将包含 null。 因此，不能使用以下常见方法来编写实体类型：

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithWarning.cs?name=CustomerWithWarning&highlight=4-5)]

[构造函数绑定](xref:core/modeling/constructors) 是一项有用的技术，可确保初始化不可为 null 的属性：

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithConstructorBinding.cs?name=CustomerWithConstructorBinding&highlight=6-9)]

遗憾的是，在某些情况下，构造函数绑定不是一个选项；例如，不能以这种方式初始化导航属性。

必需的导航属性会带来额外的难度：尽管某个给定主体的依赖项始终存在，但特定查询可能会或不加载该依赖项，具体取决于程序中该点的需求 (参阅[加载数据的不同模式](xref:core/querying/related-data))。 同时，不需要将这些属性设置为可为空，因为这会强制对它们的所有访问都检查是否是 null，即使它们是必需的。

处理这些情况的一种方法是使用一个带有可为空的[支持字段](xref:core/modeling/backing-field)的不可为空的属性：

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Order.cs?range=10-17)]

由于导航属性不可为空，因此导航被配置成必需的；只要正确加载导航，就可以通过属性访问依赖项。 但是，如果在未事先正确加载相关实体的情况下访问属性，则会引发 InvalidOperationException，因为使用了不正确的 API 约定。 请注意，必须将 EF 配置为始终访问支持字段而不是属性，因为它依赖于即使在未设置时也能读取值;请参阅有关如何执行此操作的 [支持字段](xref:core/modeling/backing-field) 的文档，并考虑指定 `PropertyAccessMode.Field` 以确保配置正确。

一种更简洁的替代方法，是使用null包容性运算符 (！ ) 简单地将属性初始化为 null：

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Order.cs?range=19)]

除非是由于编程错误，实际的 null 值永远不会被观察到，例如，访问导航属性时，没有事先正确加载相关实体。

> [!NOTE]
> 包含对多个相关实体引用的集合导航应始终不可为空。 空集合意味着不存在相关实体，但列表本身不应为 null。

## <a name="dbcontext-and-dbset"></a>DbContext 和 DbSet

在上下文类型上使用未初始化的 DbSet 属性的常见做法也有问题，因为编译器现在会发出警告。 可以按如下所示修复此问题：

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/NullableReferenceTypesContext.cs?name=Context&highlight=3-4)]

另一种策略是使用不可为 null 的自动属性，但要将其初始化为 null，请使用包容性运算符 (！ ) 使编译器不提示警告。 DbContext 基本构造函数可确保所有 DbSet 属性都将进行初始化，并且不会在这些属性上看到 null。

## <a name="navigating-and-including-nullable-relationships"></a>导航和包括可为空的关系

当处理可选关系时，可能会遇到编译器警告，虽然不可能出现实际的空引用异常。 在转换和执行 LINQ 查询时，EF Core 确保在一个可选的相关实体不存在的情况下，将忽略对该实体的任何导航，而不是引发。 但是，编译器不知道 EF Core 的这个保证，并生成警告，就好像使用LINQ to Objects 的 LINQ 查询是在内存中执行的。 因此，需要使用包容性运算符 (！ ) 来通知编译器真正的 null 值是不可能的：

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Program.cs?range=46)]

在可选导航中包含多个级别的关系时，会发生类似的问题：

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Program.cs?range=36-39&highlight=2)]

如果你发现自己执行了大量操作，并且所涉及的实体类型主要 (或独占) 用于 EF Core 查询，请考虑使导航属性不可为空，并将其配置为可通过流畅 API 或数据注解将它们配置为可选。 这将删除所有编译器警告，同时保持关系为可选;但是，如果你的实体是在 EF Core 之外遍历的，则你可能会观察到 null 值，尽管这些属性已批注为不可为空。

## <a name="limitations"></a>限制

* 反向工程当前不支持 [C# 8 可为空的引用类型 (NRTs) ](/dotnet/csharp/tutorials/nullable-reference-types)： EF Core 始终生成假定该特性处于关闭状态的 C# 代码。 例如，可为空的文本列被生成为类型为 `string` 的属性，而不是 `string?` ，无论是使用 Fluent API 还是数据批注，也无论属性被配置为必需的还是可选的。 您可以编辑脚手架代码并将其替换为 C# 的为空性注解。 问题[#15520](https://github.com/dotnet/efcore/issues/15520)跟踪了脚手架对可为空引用类型的支持。
* EF Core 的公共 API 图面尚未批注为为空性 (公共 API 为 "在意" ) ，这使得在打开 NRT 功能时，使用此功能有时会很难使用。 这特别包括 EF Core 公开的异步 LINQ 运算符，如 [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_)。 我们计划在5.0 版本解决这一情况。
