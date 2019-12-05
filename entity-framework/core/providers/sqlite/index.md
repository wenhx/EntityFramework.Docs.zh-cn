---
title: SQLite 数据库提供程序 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 3e2f7698-fec2-4cec-9e2d-2e3e0074120c
uid: core/providers/sqlite/index
ms.openlocfilehash: 433dedbe1e0e11bf2fd83e259e321ece32b2c188
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824803"
---
# <a name="sqlite-ef-core-database-provider"></a>SQLite EF Core 数据库提供程序

此数据库提供程序允许将 Entity Framework Core 与 SQLite 一起使用。 该提供程序作为 [Entity Framework Core 项目](https://github.com/aspnet/EntityFrameworkCore)的组成部分进行维护。

## <a name="install"></a>安装

安装 [Microsoft.EntityFrameworkCore.Sqlite NuGet 包](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)。

## <a name="net-core-clitabdotnet-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

## <a name="visual-studiotabvs"></a>[Visual Studio](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

***

## <a name="supported-database-engines"></a>支持的数据库引擎

* SQLite（3.7 及以上版本）

## <a name="limitations"></a>限制

有关 SQLite 提供程序的一些重要限制，请参阅 [SQLite 限制](limitations.md)。
