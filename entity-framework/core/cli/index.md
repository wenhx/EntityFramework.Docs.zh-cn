---
title: Entity Framework Core 工具参考 - EF Core
description: Entity Framework Core CLI 工具和 Visual Studio 包管理器控制台的参考指南
author: bricelam
ms.date: 09/19/2018
uid: core/cli/index
ms.openlocfilehash: 9a6ae8f945e92453ddfaa089ae1d606d142f725a
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431117"
---
# <a name="entity-framework-core-tools-reference"></a>Entity Framework Core 工具参考

Entity Framework Core 工具可帮助完成设计时的开发任务。 它们主要用于通过对数据库架构进行反向工程来管理迁移和搭建 `DbContext` 和实体类型的基架。

* 在 Visual Studio 中，[EF Core 程序包管理器控制台工具](xref:core/cli/powershell) 在[程序包管理器控制台](/nuget/tools/package-manager-console)中运行。

* [EF Core.NET 命令行接口 (CLI) 工具](xref:core/cli/dotnet)是对跨平台 [.NET Core CLI 工具](/dotnet/core/tools/)的扩展。 这些工具需要 .NET Core SDK 项目（具有 `Sdk="Microsoft.NET.Sdk"` 的项目或项目文件中的相似项目）。

这两种工具提供相同的功能。 如果使用 Visual Studio 进行开发，我们建议使用 **程序包管理器控制台** 工具，因为这些工具提供更完整的体验。

## <a name="next-steps"></a>后续步骤

* [EF Core 程序包管理器控制台工具参考](xref:core/cli/powershell)
* [EF Core.NET CLI 工具参考](xref:core/cli/dotnet)
