---
title: 从 EF6 迁移到 EF Core-移植基于 EDMX 的模型-EF
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 63003709-f1ec-4bdc-8083-65a60c4826d2
uid: efcore-and-ef6/porting/port-edmx
ms.openlocfilehash: f0bb06dc687aaa774981d97daadc55f00fbd527e
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182067"
---
# <a name="porting-an-ef6-edmx-based-model-to-ef-core"></a>移植到 EF Core 了从 EF6 基于 EDMX 的模型

EF Core 不支持的模型的 EDMX 文件格式。 移植这些模型的最佳选项是从应用程序的数据库中生成新的基于代码的模型。

## <a name="install-ef-core-nuget-packages"></a>安装 EF Core NuGet 包

安装 `Microsoft.EntityFrameworkCore.Tools` NuGet 包。

## <a name="regenerate-the-model"></a>重新生成模型

你现在可以使用 "反向工程" 功能基于现有数据库创建模型。

在 Package Manager Console 中运行以下命令（工具– > NuGet 包管理器– > 程序包管理器控制台）。 请参阅[程序包管理器控制台（Visual Studio）](../../core/miscellaneous/cli/powershell.md) ，以获取用于基架表的子集的命令选项。

``` powershell
Scaffold-DbContext "<connection string>" <database provider name>
```

例如，下面的命令从 SQL Server LocalDB 实例上的博客数据库中基架模型。

``` powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="remove-ef6-model"></a>删除 EF6 模型

现在，你将从应用程序中删除 EF6 模型。

最好将从 EF6 NuGet 包 (EntityFramework) 安装，因为 EF Core 和 ef6 更高版本可以是在同一应用程序并行使用。 但是，如果你不打算在应用程序的任何区域使用 EF6，则卸载包将有助于在需要注意的代码段上生成编译错误。

## <a name="update-your-code"></a>更新代码

此时，它是一个寻址编译错误和查看代码，以查看你会影响从 EF6 和 EF Core 之间的行为更改。

## <a name="test-the-port"></a>测试端口

只是因为你的应用程序将编译，并不意味着它成功移植到 EF Core。 需要测试应用程序的所有区域，以确保不会对应用程序产生负面影响。
