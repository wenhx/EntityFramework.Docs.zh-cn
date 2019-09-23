---
title: Microsoft SQL Server 数据库提供程序 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 2e007c82-c6e4-45bb-8129-851b79ec1a0a
uid: core/providers/sql-server/index
ms.openlocfilehash: 70e7f3d4bc1f57f1b23d9b3e0bd6264236ddbd27
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149196"
---
# <a name="microsoft-sql-server-ef-core-database-provider"></a>Microsoft SQL Server EF Core 数据库提供程序

此数据库提供程序允许将 Entity Framework Core 与 Microsoft SQL Server（包括 SQL Azure）一起使用。 该提供程序作为 [Entity Framework Core 项目](https://github.com/aspnet/EntityFrameworkCore)的组成部分进行维护。

## <a name="install"></a>安装

安装 [Microsoft.EntityFrameworkCore.SqlServer NuGet 包](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)。

``` powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="supported-database-engines"></a>支持的数据库引擎

* Microsoft SQL Server（2012 及以上版本）
