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
# <a name="entity-framework-core-tools-reference"></a><span data-ttu-id="2671b-103">Entity Framework Core 工具参考</span><span class="sxs-lookup"><span data-stu-id="2671b-103">Entity Framework Core tools reference</span></span>

<span data-ttu-id="2671b-104">Entity Framework Core 工具可帮助完成设计时的开发任务。</span><span class="sxs-lookup"><span data-stu-id="2671b-104">The Entity Framework Core tools help with design-time development tasks.</span></span> <span data-ttu-id="2671b-105">它们主要用于通过对数据库架构进行反向工程来管理迁移和搭建 `DbContext` 和实体类型的基架。</span><span class="sxs-lookup"><span data-stu-id="2671b-105">They're primarily used to manage Migrations and to scaffold a `DbContext` and entity types by reverse engineering the schema of a database.</span></span>

* <span data-ttu-id="2671b-106">在 Visual Studio 中，[EF Core 程序包管理器控制台工具](xref:core/miscellaneous/cli/powershell) 在[程序包管理器控制台](/nuget/tools/package-manager-console)中运行。</span><span class="sxs-lookup"><span data-stu-id="2671b-106">The [EF Core Package Manager Console tools](xref:core/miscellaneous/cli/powershell) run in the [Package Manager Console](/nuget/tools/package-manager-console) in Visual Studio.</span></span>

* <span data-ttu-id="2671b-107">[EF Core.NET 命令行接口 (CLI) 工具](xref:core/miscellaneous/cli/dotnet)是对跨平台 [.NET Core CLI 工具](/dotnet/core/tools/)的扩展。</span><span class="sxs-lookup"><span data-stu-id="2671b-107">The [EF Core .NET command-line interface (CLI) tools](xref:core/miscellaneous/cli/dotnet) are an extension to the cross-platform [.NET Core CLI tools](/dotnet/core/tools/).</span></span> <span data-ttu-id="2671b-108">这些工具需要 .NET Core SDK 项目（具有 `Sdk="Microsoft.NET.Sdk"` 的项目或项目文件中的相似项目）。</span><span class="sxs-lookup"><span data-stu-id="2671b-108">These tools require a .NET Core SDK project (one with `Sdk="Microsoft.NET.Sdk"` or similar in the project file).</span></span>

<span data-ttu-id="2671b-109">这两种工具提供相同的功能。</span><span class="sxs-lookup"><span data-stu-id="2671b-109">Both tools expose the same functionality.</span></span> <span data-ttu-id="2671b-110">如果使用 Visual Studio 进行开发，我们建议使用**程序包管理器控制台**工具，因为这些工具提供更完整的体验。</span><span class="sxs-lookup"><span data-stu-id="2671b-110">If you're developing in Visual Studio, we recommend using the **Package Manager Console** tools since they provide a more integrated experience.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2671b-111">后续步骤</span><span class="sxs-lookup"><span data-stu-id="2671b-111">Next steps</span></span>

* [<span data-ttu-id="2671b-112">EF Core 程序包管理器控制台工具参考</span><span class="sxs-lookup"><span data-stu-id="2671b-112">EF Core Package Manager Console tools reference</span></span>](xref:core/miscellaneous/cli/powershell)
* [<span data-ttu-id="2671b-113">EF Core.NET CLI 工具参考</span><span class="sxs-lookup"><span data-stu-id="2671b-113">EF Core .NET CLI tools reference</span></span>](xref:core/miscellaneous/cli/dotnet)
