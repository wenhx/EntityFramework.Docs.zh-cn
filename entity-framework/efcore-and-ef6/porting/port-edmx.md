---
title: 从 EF6 移植到 EF Core - 移植基于 EDMX 的模型 - EF
description: 关于将基于 Entity Framework 6 EDMX 的模型应用程序移植到 Entity Framework Core 的特定信息
author: ajcvickers
ms.date: 10/27/2016
uid: efcore-and-ef6/porting/port-edmx
ms.openlocfilehash: 27b37ad1c2e3436ae96a71bc97e953763c48ee50
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064258"
---
# <a name="porting-an-ef6-edmx-based-model-to-ef-core"></a><span data-ttu-id="dbc5d-103">将基于 EF6 EDMX 的模型移植到 EF Core</span><span class="sxs-lookup"><span data-stu-id="dbc5d-103">Porting an EF6 EDMX-Based Model to EF Core</span></span>

<span data-ttu-id="dbc5d-104">EF Core 不支持对模型使用 EDMX 文件格式。</span><span class="sxs-lookup"><span data-stu-id="dbc5d-104">EF Core does not support the EDMX file format for models.</span></span> <span data-ttu-id="dbc5d-105">要移植这些模型，最佳方法是从应用程序的数据库中生成基于代码的新模型。</span><span class="sxs-lookup"><span data-stu-id="dbc5d-105">The best option to port these models, is to generate a new code-based model from the database for your application.</span></span>

## <a name="install-ef-core-nuget-packages"></a><span data-ttu-id="dbc5d-106">安装 EF Core NuGet 包</span><span class="sxs-lookup"><span data-stu-id="dbc5d-106">Install EF Core NuGet packages</span></span>

<span data-ttu-id="dbc5d-107">安装 `Microsoft.EntityFrameworkCore.Tools` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="dbc5d-107">Install the `Microsoft.EntityFrameworkCore.Tools` NuGet package.</span></span>

## <a name="regenerate-the-model"></a><span data-ttu-id="dbc5d-108">重新生成模型</span><span class="sxs-lookup"><span data-stu-id="dbc5d-108">Regenerate the model</span></span>

<span data-ttu-id="dbc5d-109">现可使用反向工程功能基于现有数据库创建模型。</span><span class="sxs-lookup"><span data-stu-id="dbc5d-109">You can now use the reverse engineer functionality to create a model based on your existing database.</span></span>

<span data-ttu-id="dbc5d-110">在包管理器控制台（“工具”–>“NuGet 包管理器”–>“包管理器控制台”）中运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="dbc5d-110">Run the following command in Package Manager Console (Tools –> NuGet Package Manager –> Package Manager Console).</span></span> <span data-ttu-id="dbc5d-111">请参阅[包管理器控制台 (Visual Studio)](xref:core/miscellaneous/cli/powershell)，获取用于设置一部分表的基架等的命令选项。</span><span class="sxs-lookup"><span data-stu-id="dbc5d-111">See [Package Manager Console (Visual Studio)](xref:core/miscellaneous/cli/powershell) for command options to scaffold a subset of tables etc.</span></span>

```powershell
Scaffold-DbContext "<connection string>" <database provider name>
```

<span data-ttu-id="dbc5d-112">例如，以下命令用于在 SQL Server LocalDB 实例上根据博客数据库设置模型的基架。</span><span class="sxs-lookup"><span data-stu-id="dbc5d-112">For example, here is the command to scaffold a model from the Blogging database on your SQL Server LocalDB instance.</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="remove-ef6-model"></a><span data-ttu-id="dbc5d-113">删除 EF6 模型</span><span class="sxs-lookup"><span data-stu-id="dbc5d-113">Remove EF6 model</span></span>

<span data-ttu-id="dbc5d-114">现从应用程序中删除 EF6 模型。</span><span class="sxs-lookup"><span data-stu-id="dbc5d-114">You would now remove the EF6 model from your application.</span></span>

<span data-ttu-id="dbc5d-115">可将 EF6 NuGet 包 (EntityFramework) 保留在安装状态，因为 EF Core 和 EF6 可在同一应用程序中并排使用。</span><span class="sxs-lookup"><span data-stu-id="dbc5d-115">It is fine to leave the EF6 NuGet package (EntityFramework) installed, as EF Core and EF6 can be used side-by-side in the same application.</span></span> <span data-ttu-id="dbc5d-116">但是，如果你不打算在应用程序的任何区域使用 EF6，则卸载此包将有助于就需注意的代码片段生成编译错误。</span><span class="sxs-lookup"><span data-stu-id="dbc5d-116">However, if you aren't intending to use EF6 in any areas of your application, then uninstalling the package will help give compile errors on pieces of code that need attention.</span></span>

## <a name="update-your-code"></a><span data-ttu-id="dbc5d-117">更新代码</span><span class="sxs-lookup"><span data-stu-id="dbc5d-117">Update your code</span></span>

<span data-ttu-id="dbc5d-118">此时，需要处理编译错误并检查代码，以查看 EF6 与 EF Core 之间的行为变化是否会对你产生影响。</span><span class="sxs-lookup"><span data-stu-id="dbc5d-118">At this point, it's a matter of addressing compilation errors and reviewing code to see if the behavior changes between EF6 and EF Core will impact you.</span></span>

## <a name="test-the-port"></a><span data-ttu-id="dbc5d-119">测试移植</span><span class="sxs-lookup"><span data-stu-id="dbc5d-119">Test the port</span></span>

<span data-ttu-id="dbc5d-120">你的应用程序进行了编译，并不意味着它会成功移植到 EF Core。</span><span class="sxs-lookup"><span data-stu-id="dbc5d-120">Just because your application compiles, does not mean it is successfully ported to EF Core.</span></span> <span data-ttu-id="dbc5d-121">需要测试应用程序的所有区域，确保行为变化都未对你的应用程序产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="dbc5d-121">You will need to test all areas of your application to ensure that none of the behavior changes have adversely impacted your application.</span></span>
