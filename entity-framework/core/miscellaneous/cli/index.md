---
title: Entity Framework Core 工具参考 - EF Core
description: Entity Framework Core CLI 工具和 Visual Studio 包管理器控制台的参考指南
author: bricelam
ms.date: 09/19/2018
uid: core/miscellaneous/cli/index
ms.openlocfilehash: 90e8d799de8fa9426de6ac3d286bcd5458a43f0d
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92061991"
---
# <a name="entity-framework-core-tools-reference"></a>Entity Framework Core 工具参考

Entity Framework Core 工具可帮助完成设计时的开发任务。 它们主要用于通过对数据库架构进行反向工程来管理迁移和搭建 `DbContext` 和实体类型的基架。

* 在 Visual Studio 中，[EF Core 程序包管理器控制台工具](xref:core/miscellaneous/cli/powershell) 在[程序包管理器控制台](/nuget/tools/package-manager-console)中运行。

* [EF Core.NET 命令行接口 (CLI) 工具](xref:core/miscellaneous/cli/dotnet)是对跨平台 [.NET Core CLI 工具](/dotnet/core/tools/)的扩展。 这些工具需要 .NET Core SDK 项目（具有 `Sdk="Microsoft.NET.Sdk"` 的项目或项目文件中的相似项目）。

这两种工具提供相同的功能。 如果使用 Visual Studio 进行开发，我们建议使用**程序包管理器控制台**工具，因为这些工具提供更完整的体验。

## <a name="next-steps"></a>后续步骤

* [EF Core 程序包管理器控制台工具参考](xref:core/miscellaneous/cli/powershell)
* [EF Core.NET CLI 工具参考](xref:core/miscellaneous/cli/dotnet)
