---
title: 全局查询筛选器 - EF Core
author: anpete
ms.date: 11/03/2017
uid: core/querying/filters
ms.openlocfilehash: 9262ff7970b0502945480c673315071cbc3f44b9
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2020
ms.locfileid: "78413761"
---
# <a name="global-query-filters"></a>全局查询筛选器

> [!NOTE]
> EF Core 2.0 中已引入此功能。

全局查询筛选器是应用于元数据模型（通常为 *OnModelCreating*）中的实体类型的 LINQ 查询谓词（通常传递给 LINQ *Where* 查询运算符的布尔表达式）。 此类筛选器自动应用于涉及这些实体类型（包括通过使用 Include 或直接导航属性引用等方式间接引用的实体类型）的所有 LINQ 查询。 此功能的一些常见应用如下：

* **软删除** - 实体类型定义“IsDeleted”  属性。
* **多租户** - 实体类型定义“TenantId”  属性。

## <a name="example"></a>示例

下面的示例显示了如何使用全局查询筛选器在简单的博客模型中实现软删除和多租户查询行为。

> [!TIP]
> 可在 GitHub 上查看此文章的[示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters)。

首先，定义实体：

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Entities)]

请注意 _Blog_ 实体上的 _tenantId_ 字段的声明。 这会用于将每个 _Blog_ 实例与特定租户相关联。 还会定义_文章_实体类型上的 _IsDeleted_ 属性。 这会用于跟踪文章  实例是否已“软删除”。 也就是说，实例标记为已删除，而实际上不会删除基础数据。

接下来，使用 `HasQueryFilter` API 在 _OnModelCreating_ 中配置查询筛选器。

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Configuration)]

传递给 _HasQueryFilter_ 调用的谓词表达式将立即自动应用于这些类型的任何 LINQ 查询。

> [!TIP]
> 请注意 DbContext 实例级别字段的使用：`_tenantId` 用于设置当前租户。 模型级筛选器将使用正确上下文实例（即执行查询的实例）中的值。

> [!NOTE]
> 目前不能在同一个实体中定义多个查询筛选器，只会应用最后一个筛选器。 但是，可以使用逻辑 _AND_ 运算符（[C# 中为 `&&`](https://docs.microsoft.com/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)）定义含有多种条件的单个筛选器。

## <a name="disabling-filters"></a>禁用筛选器

可使用 `IgnoreQueryFilters()` 运算符对各个 LINQ 查询禁用筛选器。

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a>限制

全局查询筛选器具有以下限制：

* 仅可为继承层次结构中的根实体类型定义筛选器。
