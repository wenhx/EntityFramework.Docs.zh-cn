---
title: Microsoft SQL Server 数据库提供程序 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 2e007c82-c6e4-45bb-8129-851b79ec1a0a
uid: core/providers/sql-server/index
ms.openlocfilehash: 1e75bc4bf334b1a60d13a2ec9ef314e3afcf0273
ms.sourcegitcommit: 2355447d89496a8ca6bcbfc0a68a14a0bf7f0327
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72812102"
---
# <a name="microsoft-sql-server-ef-core-database-provider"></a>Microsoft SQL Server EF Core 数据库提供程序

此数据库提供程序允许将 Entity Framework Core 与 Microsoft SQL Server（包括 SQL Azure）一起使用。 该提供程序作为 [Entity Framework Core 项目](https://github.com/aspnet/EntityFrameworkCore)的组成部分进行维护。

## <a name="install"></a>安装

安装 [Microsoft.EntityFrameworkCore.SqlServer NuGet 包](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)。

# <a name="net-core-clitabdotnet-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

``` console
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

# <a name="visual-studiotabvs"></a>[Visual Studio](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

***

> [!NOTE]
> 自版本 3.0.0 起，提供程序引用 Microsoft.Data.SqlClient（以前的版本依赖于 SqlClient）。 如果项目直接依赖于 SqlClient，请确保它引用了正确的包。

## <a name="supported-database-engines"></a>支持的数据库引擎

* Microsoft SQL Server（2012 及以上版本）
