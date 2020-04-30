---
title: 用 EF 内存中数据库进行测试-EF Core
author: ajcvickers
description: 使用 EF 内存中数据库测试 EF Core 应用程序
ms.date: 10/27/2016
uid: core/miscellaneous/testing/in-memory
ms.openlocfilehash: f31b3bdedb8c339dbb6baa9d7f2031d023d5757f
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82538352"
---
# <a name="testing-with-the-ef-in-memory-database"></a><span data-ttu-id="1b0b2-103">用 EF 内存中数据库进行测试</span><span class="sxs-lookup"><span data-stu-id="1b0b2-103">Testing with the EF In-Memory Database</span></span>

> [!WARNING]
> <span data-ttu-id="1b0b2-104">EF 内存中数据库的行为通常与关系数据库不同。</span><span class="sxs-lookup"><span data-stu-id="1b0b2-104">The EF in-memory database often behaves differently than relational databases.</span></span>
> <span data-ttu-id="1b0b2-105">在充分了解所涉及的问题和折衷后，只需使用 EF 内存中数据库，如[测试使用 EF Core 的代码](xref:core/miscellaneous/testing/index)中所述。</span><span class="sxs-lookup"><span data-stu-id="1b0b2-105">Only use the EF in-memory database after fully understanding the issues and trade-offs involved, as discussed in [Testing code that uses EF Core](xref:core/miscellaneous/testing/index).</span></span>  

> [!TIP]
> <span data-ttu-id="1b0b2-106">SQLite 是一个关系提供程序，还可以使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="1b0b2-106">SQLite is a relational provider and can also use in-memory databases.</span></span>
> <span data-ttu-id="1b0b2-107">请考虑使用它进行测试，以便更密切地匹配常见的关系数据库行为。</span><span class="sxs-lookup"><span data-stu-id="1b0b2-107">Consider using this for testing to more closely match common relational database behaviors.</span></span>
> <span data-ttu-id="1b0b2-108">[使用 SQLite 测试 EF Core 的应用程序中对](xref:core/miscellaneous/testing/sqlite)此进行了介绍。</span><span class="sxs-lookup"><span data-stu-id="1b0b2-108">This is covered in [Using SQLite to test an EF Core application](xref:core/miscellaneous/testing/sqlite).</span></span>   

<span data-ttu-id="1b0b2-109">此页上的信息现在驻留在其他位置：</span><span class="sxs-lookup"><span data-stu-id="1b0b2-109">The information on this page now lives in other locations:</span></span>
* <span data-ttu-id="1b0b2-110">有关使用 EF 内存中数据库进行测试的常规信息，请参阅[测试使用 EF Core 的代码](xref:core/miscellaneous/testing/index)。</span><span class="sxs-lookup"><span data-stu-id="1b0b2-110">See [Testing code that uses EF Core](xref:core/miscellaneous/testing/index) for general information on testing with the EF in-memory database.</span></span>
* <span data-ttu-id="1b0b2-111">请参阅示例，演示如何使用 EF 内存中数据库[测试使用 EF Core 的示例应用程序](xref:core/miscellaneous/testing/testing-sample)。</span><span class="sxs-lookup"><span data-stu-id="1b0b2-111">See [Sample showing how to test applications that use EF Core](xref:core/miscellaneous/testing/testing-sample) for a sample using the EF in-memory database.</span></span>
* <span data-ttu-id="1b0b2-112">有关 EF 内存中数据库的常规信息，请参阅[ef 内存中数据库提供程序](xref:core/providers/in-memory/index)。</span><span class="sxs-lookup"><span data-stu-id="1b0b2-112">See [The EF in-memory database provider](xref:core/providers/in-memory/index) for general information about the EF in-memory database.</span></span>
