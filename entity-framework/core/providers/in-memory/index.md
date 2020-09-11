---
title: InMemory 数据库提供程序 - EF Core
description: 有关 Entity Framework Core InMemory 数据库提供程序的信息
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 9af0cba7-7605-4f8f-9cfa-dd616fcb880c
uid: core/providers/in-memory/index
ms.openlocfilehash: 6af1d61a6ff76b82cc0096edbf095a6338d21109
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619006"
---
# <a name="ef-core-in-memory-database-provider"></a>EF Core In-Memory 数据库提供程序

此数据库提供程序允许将 Entity Framework Core 和内存数据库一起使用。 这对测试非常有用，尽管内存中模式下的 SQLite 提供程序可能是针对关系数据库的更合适的测试替代。 该提供程序作为 [Entity Framework Core 项目](https://github.com/aspnet/EntityFrameworkCore)的组成部分进行维护。

## <a name="install"></a>安装

安装 [Microsoft.EntityFrameworkCore.InMemory NuGet 包](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.InMemory
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.InMemory
```

***

## <a name="get-started"></a>入门

下列资源可帮助你开始使用此提供程序。

* [使用 InMemory 进行测试](xref:core/miscellaneous/testing/in-memory)
* [UnicornStore 示例应用程序测试](https://github.com/rowanmiller/UnicornStore/blob/master/UnicornStore/src/UnicornStore.Tests/Controllers/ShippingControllerTests.cs)

## <a name="supported-database-engines"></a>支持的数据库引擎

进程内内存数据库（仅用于测试目的）
