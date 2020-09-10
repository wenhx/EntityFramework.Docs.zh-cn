---
title: 用 EF 内存中数据库进行测试-EF Core
description: 使用 EF 内存中数据库测试 Entity Framework Core 应用程序
author: ajcvickers
ms.date: 10/27/2016
uid: core/miscellaneous/testing/in-memory
ms.openlocfilehash: 353f67fb0e78fefa74fc77d302e505bacb692ed4
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619391"
---
# <a name="testing-with-the-ef-in-memory-database"></a>用 EF 内存中数据库进行测试

> [!WARNING]
> EF 内存中数据库的行为通常与关系数据库不同。
> 在充分了解所涉及的问题和折衷后，只需使用 EF 内存中数据库，如 [测试使用 EF Core 的代码](xref:core/miscellaneous/testing/index)中所述。  

> [!TIP]
> SQLite 是一个关系提供程序，还可以使用内存中数据库。
> 请考虑使用它进行测试，以便更密切地匹配常见的关系数据库行为。
> [使用 SQLite 测试 EF Core 的应用程序中对](xref:core/miscellaneous/testing/sqlite)此进行了介绍。   

此页上的信息现在驻留在其他位置：
* 有关使用 EF 内存中数据库进行测试的常规信息，请参阅 [测试使用 EF Core 的代码](xref:core/miscellaneous/testing/index) 。
* 请参阅示例，演示如何使用 EF 内存中数据库 [测试使用 EF Core 的示例应用程序](xref:core/miscellaneous/testing/testing-sample) 。
* 有关 EF 内存中数据库的常规信息，请参阅 [ef 内存中数据库提供程序](xref:core/providers/in-memory/index) 。
