---
title: 从 EF6 移植到 EF Core - 移植基于 EDMX 的模型 - EF
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 63003709-f1ec-4bdc-8083-65a60c4826d2
uid: efcore-and-ef6/porting/port-edmx
ms.openlocfilehash: f0bb06dc687aaa774981d97daadc55f00fbd527e
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2020
ms.locfileid: "78413527"
---
# <a name="porting-an-ef6-edmx-based-model-to-ef-core"></a><span data-ttu-id="47077-102">将基于 EF6 EDMX 的模型移植到 EF Core</span><span class="sxs-lookup"><span data-stu-id="47077-102">Porting an EF6 EDMX-Based Model to EF Core</span></span>

<span data-ttu-id="47077-103">EF Core 不支持对模型使用 EDMX 文件格式。</span><span class="sxs-lookup"><span data-stu-id="47077-103">EF Core does not support the EDMX file format for models.</span></span> <span data-ttu-id="47077-104">要移植这些模型，最佳方法是从应用程序的数据库中生成基于代码的新模型。</span><span class="sxs-lookup"><span data-stu-id="47077-104">The best option to port these models, is to generate a new code-based model from the database for your application.</span></span>

## <a name="install-ef-core-nuget-packages"></a><span data-ttu-id="47077-105">安装 EF Core NuGet 包</span><span class="sxs-lookup"><span data-stu-id="47077-105">Install EF Core NuGet packages</span></span>

<span data-ttu-id="47077-106">安装 `Microsoft.EntityFrameworkCore.Tools` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="47077-106">Install the `Microsoft.EntityFrameworkCore.Tools` NuGet package.</span></span>

## <a name="regenerate-the-model"></a><span data-ttu-id="47077-107">重新生成模型</span><span class="sxs-lookup"><span data-stu-id="47077-107">Regenerate the model</span></span>

<span data-ttu-id="47077-108">现可使用反向工程功能基于现有数据库创建模型。</span><span class="sxs-lookup"><span data-stu-id="47077-108">You can now use the reverse engineer functionality to create a model based on your existing database.</span></span>

<span data-ttu-id="47077-109">在包管理器控制台（“工具”–>“NuGet 包管理器”–>“包管理器控制台”）中运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="47077-109">Run the following command in Package Manager Console (Tools –> NuGet Package Manager –> Package Manager Console).</span></span> <span data-ttu-id="47077-110">请参阅[包管理器控制台 (Visual Studio)](../../core/miscellaneous/cli/powershell.md)，获取用于设置一部分表的基架等的命令选项。</span><span class="sxs-lookup"><span data-stu-id="47077-110">See [Package Manager Console (Visual Studio)](../../core/miscellaneous/cli/powershell.md) for command options to scaffold a subset of tables etc.</span></span>

``` powershell
Scaffold-DbContext "<connection string>" <database provider name>
```

<span data-ttu-id="47077-111">例如，以下命令用于在 SQL Server LocalDB 实例上根据博客数据库设置模型的基架。</span><span class="sxs-lookup"><span data-stu-id="47077-111">For example, here is the command to scaffold a model from the Blogging database on your SQL Server LocalDB instance.</span></span>

``` powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="remove-ef6-model"></a><span data-ttu-id="47077-112">删除 EF6 模型</span><span class="sxs-lookup"><span data-stu-id="47077-112">Remove EF6 model</span></span>

<span data-ttu-id="47077-113">现从应用程序中删除 EF6 模型。</span><span class="sxs-lookup"><span data-stu-id="47077-113">You would now remove the EF6 model from your application.</span></span>

<span data-ttu-id="47077-114">可将 EF6 NuGet 包 (EntityFramework) 保留在安装状态，因为 EF Core 和 EF6 可在同一应用程序中并排使用。</span><span class="sxs-lookup"><span data-stu-id="47077-114">It is fine to leave the EF6 NuGet package (EntityFramework) installed, as EF Core and EF6 can be used side-by-side in the same application.</span></span> <span data-ttu-id="47077-115">但是，如果你不打算在应用程序的任何区域使用 EF6，则卸载此包将有助于就需注意的代码片段生成编译错误。</span><span class="sxs-lookup"><span data-stu-id="47077-115">However, if you aren't intending to use EF6 in any areas of your application, then uninstalling the package will help give compile errors on pieces of code that need attention.</span></span>

## <a name="update-your-code"></a><span data-ttu-id="47077-116">更新代码</span><span class="sxs-lookup"><span data-stu-id="47077-116">Update your code</span></span>

<span data-ttu-id="47077-117">此时，需要处理编译错误并检查代码，以查看 EF6 与 EF Core 之间的行为变化是否会对你产生影响。</span><span class="sxs-lookup"><span data-stu-id="47077-117">At this point, it's a matter of addressing compilation errors and reviewing code to see if the behavior changes between EF6 and EF Core will impact you.</span></span>

## <a name="test-the-port"></a><span data-ttu-id="47077-118">测试移植</span><span class="sxs-lookup"><span data-stu-id="47077-118">Test the port</span></span>

<span data-ttu-id="47077-119">你的应用程序进行了编译，并不意味着它会成功移植到 EF Core。</span><span class="sxs-lookup"><span data-stu-id="47077-119">Just because your application compiles, does not mean it is successfully ported to EF Core.</span></span> <span data-ttu-id="47077-120">需要测试应用程序的所有区域，确保行为变化都未对你的应用程序产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="47077-120">You will need to test all areas of your application to ensure that none of the behavior changes have adversely impacted your application.</span></span>
