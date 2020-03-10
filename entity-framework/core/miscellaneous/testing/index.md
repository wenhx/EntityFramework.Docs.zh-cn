---
title: 使用 EF Core 测试组件 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 1603be0c-69bc-4dd9-9a08-3d0129cdc6c1
uid: core/miscellaneous/testing/index
ms.openlocfilehash: 1ca900528ed42ad4b41016f22964c3494b0286eb
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78412792"
---
# <a name="testing-components-using-ef-core"></a><span data-ttu-id="56300-102">正在使用 EF Core 测试组件</span><span class="sxs-lookup"><span data-stu-id="56300-102">Testing components using EF Core</span></span>

<span data-ttu-id="56300-103">你可能希望使用类似于连接真实数据库的方式来测试组件，但不产生实际数据库 I/O 操作的开销。</span><span class="sxs-lookup"><span data-stu-id="56300-103">You may want to test components using something that approximates connecting to the real database, without the overhead of actual database I/O operations.</span></span>

<span data-ttu-id="56300-104">可通过以下两种主要方法执行此操作：</span><span class="sxs-lookup"><span data-stu-id="56300-104">There are two main options for doing this:</span></span>

* <span data-ttu-id="56300-105">通过 [SQLite 内存中模式](sqlite.md)，可针对行为类似于关系数据库的提供程序编写有效测试。</span><span class="sxs-lookup"><span data-stu-id="56300-105">[SQLite in-memory mode](sqlite.md) allows you to write efficient tests against a provider that behaves like a relational database.</span></span>
* <span data-ttu-id="56300-106">[InMemory 提供程序](in-memory.md)是一个具有极少依赖项的轻量提供程序，但其行为不会始终与关系数据库相似。</span><span class="sxs-lookup"><span data-stu-id="56300-106">[The InMemory provider](in-memory.md) is a lightweight provider that has minimal dependencies, but does not always behave like a relational database.</span></span>
