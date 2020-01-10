---
title: 测试以及 InMemory 的 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 0d0590f1-1ea3-4d5c-8f44-db17395cd3f3
uid: core/miscellaneous/testing/in-memory
ms.openlocfilehash: fcd2f99ad06fd30ef9e36fd1e5a6a09fe0a45d07
ms.sourcegitcommit: 4e86f01740e407ff25e704a11b1f7d7e66bfb2a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75781113"
---
# <a name="testing-with-inmemory"></a>使用 InMemory 进行测试

如果要使用与连接到实际数据库类似的内容来测试组件，而不是实际数据库操作的开销，则 InMemory 提供程序很有用。

> [!TIP]  
> 可在 GitHub 上查看此文章的[示例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing)。

## <a name="inmemory-is-not-a-relational-database"></a>InMemory 不是关系数据库

EF Core 数据库提供程序不需要是关系数据库。 InMemory 旨在作为一般用途的数据库进行测试，而不是为模拟关系数据库而设计。

其中的一些示例包括：

* InMemory 允许您保存违反关系数据库中的引用完整性约束的数据。
* 如果对模型中的属性使用 DefaultValueSql （string），则这是一个关系数据库 API，在对 InMemory 运行时将不起作用。
* 不支持[通过时间戳/行版本](xref:core/modeling/concurrency#timestamprowversion)（`[Timestamp]` 或 `IsRowVersion`）的并发。 如果使用旧并发标记完成更新，将不会引发[DbUpdateConcurrencyException](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception) 。

> [!TIP]  
> 很多测试目的都不重要。 但是，如果您想要对行为更像是真实关系数据库的某些内容进行测试，则应考虑使用[SQLite 内存中模式](sqlite.md)。

## <a name="example-testing-scenario"></a>示例测试方案

请考虑以下允许应用程序代码执行一些与博客相关的操作的服务。 该服务在内部使用连接到 SQL Server 数据库的 `DbContext`。 交换此上下文以连接到 InMemory 数据库会很有用，这样就可以为此服务编写高效的测试，而无需修改代码，或执行大量工作来创建上下文的测试双精度。

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

使用此提供程序进行测试的关键是能够通知上下文使用 InMemory 提供程序，并控制内存中数据库的范围。 通常，每个测试方法都需要一个干净的数据库。

下面是使用 InMemory 数据库的测试类的示例。 每个测试方法均指定唯一的数据库名称，也就是说，每个方法都有其自己的 InMemory 数据库。

>[!TIP]
> 若要使用 `.UseInMemoryDatabase()` 扩展方法，请参考 NuGet 包[microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/TestProject/InMemory/BlogServiceTests.cs)]
