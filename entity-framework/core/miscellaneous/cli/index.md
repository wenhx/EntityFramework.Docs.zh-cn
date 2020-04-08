---
title: Entity Framework Core 工具参考 - EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/19/2018
uid: core/miscellaneous/cli/index
ms.openlocfilehash: 237192c55ea3542521a7a292ac8550d72e4ef82c
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2020
ms.locfileid: "78412812"
---
# <a name="entity-framework-core-tools-reference"></a><span data-ttu-id="35711-102">Entity Framework Core 工具参考</span><span class="sxs-lookup"><span data-stu-id="35711-102">Entity Framework Core tools reference</span></span>

<span data-ttu-id="35711-103">Entity Framework Core 工具可帮助完成设计时的开发任务。</span><span class="sxs-lookup"><span data-stu-id="35711-103">The Entity Framework Core tools help with design-time development tasks.</span></span> <span data-ttu-id="35711-104">它们主要用于通过对数据库架构进行反向工程来管理迁移和搭建 `DbContext` 和实体类型的基架。</span><span class="sxs-lookup"><span data-stu-id="35711-104">They're primarily used to manage Migrations and to scaffold a `DbContext` and entity types by reverse engineering the schema of a database.</span></span>

* <span data-ttu-id="35711-105">在 Visual Studio 中，[EF Core 程序包管理器控制台工具](powershell.md) 在[程序包管理器控制台](https://docs.microsoft.com/nuget/tools/package-manager-console)中运行。</span><span class="sxs-lookup"><span data-stu-id="35711-105">The [EF Core Package Manager Console tools](powershell.md) run in the [Package Manager Console](https://docs.microsoft.com/nuget/tools/package-manager-console) in Visual Studio.</span></span>

* <span data-ttu-id="35711-106">[EF Core.NET 命令行接口 (CLI) 工具](dotnet.md)是对跨平台 [.NET Core CLI 工具](https://docs.microsoft.com/dotnet/core/tools/)的扩展。</span><span class="sxs-lookup"><span data-stu-id="35711-106">The [EF Core .NET command-line interface (CLI) tools](dotnet.md) are an extension to the cross-platform [.NET Core CLI tools](https://docs.microsoft.com/dotnet/core/tools/).</span></span> <span data-ttu-id="35711-107">这些工具需要 .NET Core SDK 项目（具有 `Sdk="Microsoft.NET.Sdk"` 的项目或项目文件中的相似项目）。</span><span class="sxs-lookup"><span data-stu-id="35711-107">These tools require a .NET Core SDK project (one with `Sdk="Microsoft.NET.Sdk"` or similar in the project file).</span></span>

<span data-ttu-id="35711-108">这两种工具提供相同的功能。</span><span class="sxs-lookup"><span data-stu-id="35711-108">Both tools expose the same functionality.</span></span> <span data-ttu-id="35711-109">如果使用 Visual Studio 进行开发，我们建议使用**程序包管理器控制台**工具，因为这些工具提供更完整的体验。</span><span class="sxs-lookup"><span data-stu-id="35711-109">If you're developing in Visual Studio, we recommend using the **Package Manager Console** tools since they provide a more integrated experience.</span></span>

## <a name="next-steps"></a><span data-ttu-id="35711-110">后续步骤</span><span class="sxs-lookup"><span data-stu-id="35711-110">Next steps</span></span>

* [<span data-ttu-id="35711-111">EF Core 程序包管理器控制台工具参考</span><span class="sxs-lookup"><span data-stu-id="35711-111">EF Core Package Manager Console tools reference</span></span>](powershell.md)
* [<span data-ttu-id="35711-112">EF Core.NET CLI 工具参考</span><span class="sxs-lookup"><span data-stu-id="35711-112">EF Core .NET CLI tools reference</span></span>](dotnet.md)
