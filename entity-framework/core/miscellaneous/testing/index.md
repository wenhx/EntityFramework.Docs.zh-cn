---
title: 使用 EF Core 测试组件
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 1603be0c-69bc-4dd9-9a08-3d0129cdc6c1
uid: core/miscellaneous/testing/index
ms.openlocfilehash: a946387718546f14e1485b4093e6c8046188f62d
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197491"
---
# <a name="testing-components-using-ef-core"></a><span data-ttu-id="49f81-102">正在使用 EF Core 测试组件</span><span class="sxs-lookup"><span data-stu-id="49f81-102">Testing components using EF Core</span></span>

<span data-ttu-id="49f81-103">你可能希望使用类似于连接真实数据库的方式来测试组件，但不产生实际数据库 I/O 操作的开销。</span><span class="sxs-lookup"><span data-stu-id="49f81-103">You may want to test components using something that approximates connecting to the real database, without the overhead of actual database I/O operations.</span></span>

<span data-ttu-id="49f81-104">可通过以下两种主要方法执行此操作：</span><span class="sxs-lookup"><span data-stu-id="49f81-104">There are two main options for doing this:</span></span>
 * <span data-ttu-id="49f81-105">通过 [SQLite 内存中模式](sqlite.md)，可针对行为类似于关系数据库的提供程序编写有效测试。</span><span class="sxs-lookup"><span data-stu-id="49f81-105">[SQLite in-memory mode](sqlite.md) allows you to write efficient tests against a provider that behaves like a relational database.</span></span>
 * <span data-ttu-id="49f81-106">[InMemory 提供程序](in-memory.md)是一个具有极少依赖项的轻量提供程序，但其行为不会始终与关系数据库相似。</span><span class="sxs-lookup"><span data-stu-id="49f81-106">[The InMemory provider](in-memory.md) is a lightweight provider that has minimal dependencies, but does not always behave like a relational database.</span></span>
