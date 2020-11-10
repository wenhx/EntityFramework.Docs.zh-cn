---
title: 从 EF6 移植到 EF Core - 移植基于 EDMX 的模型 - EF
description: 关于将基于 Entity Framework 6 EDMX 的模型应用程序移植到 Entity Framework Core 的特定信息
author: ajcvickers
ms.date: 10/27/2016
uid: efcore-and-ef6/porting/port-edmx
ms.openlocfilehash: 9c1b308318c9bf0325ece0b60ace646becc8de39
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429216"
---
# <a name="porting-an-ef6-edmx-based-model-to-ef-core"></a>将基于 EF6 EDMX 的模型移植到 EF Core

EF Core 不支持对模型使用 EDMX 文件格式。 要移植这些模型，最佳方法是从应用程序的数据库中生成基于代码的新模型。

## <a name="install-ef-core-nuget-packages"></a>安装 EF Core NuGet 包

安装 `Microsoft.EntityFrameworkCore.Tools` NuGet 包。

## <a name="regenerate-the-model"></a>重新生成模型

现可使用反向工程功能基于现有数据库创建模型。

在包管理器控制台（“工具”–>“NuGet 包管理器”–>“包管理器控制台”）中运行以下命令。 请参阅[包管理器控制台 (Visual Studio)](xref:core/cli/powershell)，获取用于设置一部分表的基架等的命令选项。

```powershell
Scaffold-DbContext "<connection string>" <database provider name>
```

例如，以下命令用于在 SQL Server LocalDB 实例上根据博客数据库设置模型的基架。

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="remove-ef6-model"></a>删除 EF6 模型

现从应用程序中删除 EF6 模型。

可将 EF6 NuGet 包 (EntityFramework) 保留在安装状态，因为 EF Core 和 EF6 可在同一应用程序中并排使用。 但是，如果你不打算在应用程序的任何区域使用 EF6，则卸载此包将有助于就需注意的代码片段生成编译错误。

## <a name="update-your-code"></a>更新代码

此时，需要处理编译错误并检查代码，以查看 EF6 与 EF Core 之间的行为变化是否会对你产生影响。

## <a name="test-the-port"></a>测试移植

你的应用程序进行了编译，并不意味着它会成功移植到 EF Core。 需要测试应用程序的所有区域，确保行为变化都未对你的应用程序产生负面影响。
