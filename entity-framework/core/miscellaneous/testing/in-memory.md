---
title: 测试以及 InMemory 的 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 0d0590f1-1ea3-4d5c-8f44-db17395cd3f3
uid: core/miscellaneous/testing/in-memory
ms.openlocfilehash: 8aaea52f22954ef6a2b7d9b9c5627597c61ac644
ms.sourcegitcommit: 8f801993c9b8cd8a8fbfa7134818a8edca79e31a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2019
ms.locfileid: "59562541"
---
# <a name="testing-with-inmemory"></a>使用 InMemory 进行测试

InMemory 提供程序时，你想要测试组件使用类似于连接真实数据库，而无需实际的数据库操作的开销。

> [!TIP]  
> 可在 GitHub 上查看此文章的[示例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing)。

## <a name="inmemory-is-not-a-relational-database"></a>InMemory 不是关系数据库

EF Core 数据库提供程序不需要是关系数据库。 InMemory 设计为通用数据库进行测试，而不是要模拟关系数据库。

包括一些示例：

* InMemory 将允许您将会违反引用完整性约束关系数据库中的数据保存。
* 如果在模型中的属性使用 DefaultValueSql(string)，这是一个关系数据库 API，不会影响针对 InMemory 运行时。
* [通过时间戳/行版本的并发](xref:core/modeling/concurrency#timestamprow-version)(`[Timestamp]`或`IsRowVersion`) 不受支持。 否[DbUpdateConcurrencyException](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception)如果更新执行此操作将引发使用旧的并发标记。

> [!TIP]  
> 对于许多测试目的这些差异将不起作用。 但是，如果你想要针对的表现得更像真正的关系数据库进行测试，请考虑使用[SQLite 内存中模式](sqlite.md)。

## <a name="example-testing-scenario"></a>示例测试方案

请考虑以下允许应用程序代码执行一些与博客相关的操作的服务。 该服务在内部使用连接到 SQL Server 数据库的 `DbContext`。 可以用来交换此上下文，以便我们可以编写此服务的有效测试，而无需修改代码，或执行大量工作来创建测试连接到 InMemory 数据库上下文的双精度。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BlogService.cs)]

## <a name="get-your-context-ready"></a>准备您的上下文

### <a name="avoid-configuring-two-database-providers"></a>避免配置两个数据库提供程序

在测试中，从外部配置上下文以使用 InMemory 提供程序。 如果要通过在上下文中替代 `OnConfiguring` 来配置数据库提供程序，则需要添加一些条件代码，从而确保只有在尚未配置数据库提供程序的情况下才进行配置。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#OnConfiguring)]

> [!TIP]  
> 如果你使用的 ASP.NET Core，则应不需要此代码由于外部 （会在 Startup.cs) 的上下文已配置数据库提供程序。

### <a name="add-a-constructor-for-testing"></a>添加用于测试的构造函数

若要启用针对不同的数据库的测试的最简单方法是修改上下文，以公开一个构造函数接受`DbContextOptions<TContext>`。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#Constructors)]

> [!TIP]  
> `DbContextOptions<TContext>` 告知上下文其所有的设置，例如要连接的数据库。 这与在上下文中运行 OnConfiguring 方法所生成的对象相同。

## <a name="writing-tests"></a>编写测试

使用此提供程序进行测试的关键是可以让使用 InMemory 提供程序，并控制内存中数据库的作用域的上下文。 通常，每个测试方法都需要一个干净的数据库。

下面是使用 InMemory 数据库的测试类的示例。 每个测试方法指定一个唯一的数据库名称，表示每个方法都有其自己的 InMemory 数据库。

>[!TIP]
> 若要使用`.UseInMemoryDatabase()`扩展方法，引用的 NuGet 包[Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/TestProject/InMemory/BlogServiceTests.cs)]
