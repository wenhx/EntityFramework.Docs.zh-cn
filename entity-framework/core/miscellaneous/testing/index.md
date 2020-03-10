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
# <a name="testing-components-using-ef-core"></a>正在使用 EF Core 测试组件

你可能希望使用类似于连接真实数据库的方式来测试组件，但不产生实际数据库 I/O 操作的开销。

可通过以下两种主要方法执行此操作：

* 通过 [SQLite 内存中模式](sqlite.md)，可针对行为类似于关系数据库的提供程序编写有效测试。
* [InMemory 提供程序](in-memory.md)是一个具有极少依赖项的轻量提供程序，但其行为不会始终与关系数据库相似。
