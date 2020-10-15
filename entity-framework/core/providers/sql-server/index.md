---
title: Microsoft SQL Server 数据库提供程序 - EF Core
description: 数据库提供程序的文档，该提供程序允许将 Entity Framework Core 与 Microsoft SQL Server 一起使用
author: AndriySvyryd
ms.date: 11/05/2019
uid: core/providers/sql-server/index
ms.openlocfilehash: 344bf243577d39d2d5a03cf321f8d84c275d10d3
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065324"
---
# <a name="microsoft-sql-server-ef-core-database-provider"></a>Microsoft SQL Server EF Core 数据库提供程序

此数据库提供程序允许将 Entity Framework Core 与 Microsoft SQL Server（包括 Azure SQL 数据库）一起使用。 该提供程序作为 [Entity Framework Core 项目](https://github.com/aspnet/EntityFrameworkCore)的组成部分进行维护。

## <a name="install"></a>安装

安装 [Microsoft.EntityFrameworkCore.SqlServer NuGet 包](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

***

> [!NOTE]
> 自版本 3.0.0 起，提供程序引用 Microsoft.Data.SqlClient（以前的版本依赖于 SqlClient）。 如果项目直接依赖于 SqlClient，请确保它引用了 Microsoft.Data.SqlClient 包。

## <a name="supported-database-engines"></a>支持的数据库引擎

* Microsoft SQL Server（2012 及以上版本）
