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
# <a name="porting-an-ef6-edmx-based-model-to-ef-core"></a><span data-ttu-id="b5423-102">移植到 EF Core 了从 EF6 基于 EDMX 的模型</span><span class="sxs-lookup"><span data-stu-id="b5423-102">Porting an EF6 EDMX-Based Model to EF Core</span></span>

<span data-ttu-id="b5423-103">EF Core 不支持的模型的 EDMX 文件格式。</span><span class="sxs-lookup"><span data-stu-id="b5423-103">EF Core does not support the EDMX file format for models.</span></span> <span data-ttu-id="b5423-104">移植这些模型的最佳选项是从应用程序的数据库中生成新的基于代码的模型。</span><span class="sxs-lookup"><span data-stu-id="b5423-104">The best option to port these models, is to generate a new code-based model from the database for your application.</span></span>

## <a name="install-ef-core-nuget-packages"></a><span data-ttu-id="b5423-105">安装 EF Core NuGet 包</span><span class="sxs-lookup"><span data-stu-id="b5423-105">Install EF Core NuGet packages</span></span>

<span data-ttu-id="b5423-106">安装 `Microsoft.EntityFrameworkCore.Tools` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="b5423-106">Install the `Microsoft.EntityFrameworkCore.Tools` NuGet package.</span></span>

## <a name="regenerate-the-model"></a><span data-ttu-id="b5423-107">重新生成模型</span><span class="sxs-lookup"><span data-stu-id="b5423-107">Regenerate the model</span></span>

<span data-ttu-id="b5423-108">你现在可以使用 "反向工程" 功能基于现有数据库创建模型。</span><span class="sxs-lookup"><span data-stu-id="b5423-108">You can now use the reverse engineer functionality to create a model based on your existing database.</span></span>

<span data-ttu-id="b5423-109">在 Package Manager Console 中运行以下命令（工具– > NuGet 包管理器– > 程序包管理器控制台）。</span><span class="sxs-lookup"><span data-stu-id="b5423-109">Run the following command in Package Manager Console (Tools –> NuGet Package Manager –> Package Manager Console).</span></span> <span data-ttu-id="b5423-110">请参阅[程序包管理器控制台（Visual Studio）](../../core/miscellaneous/cli/powershell.md) ，以获取用于基架表的子集的命令选项。</span><span class="sxs-lookup"><span data-stu-id="b5423-110">See [Package Manager Console (Visual Studio)](../../core/miscellaneous/cli/powershell.md) for command options to scaffold a subset of tables etc.</span></span>

``` powershell
Scaffold-DbContext "<connection string>" <database provider name>
```

<span data-ttu-id="b5423-111">例如，下面的命令从 SQL Server LocalDB 实例上的博客数据库中基架模型。</span><span class="sxs-lookup"><span data-stu-id="b5423-111">For example, here is the command to scaffold a model from the Blogging database on your SQL Server LocalDB instance.</span></span>

``` powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="remove-ef6-model"></a><span data-ttu-id="b5423-112">删除 EF6 模型</span><span class="sxs-lookup"><span data-stu-id="b5423-112">Remove EF6 model</span></span>

<span data-ttu-id="b5423-113">现在，你将从应用程序中删除 EF6 模型。</span><span class="sxs-lookup"><span data-stu-id="b5423-113">You would now remove the EF6 model from your application.</span></span>

<span data-ttu-id="b5423-114">最好将从 EF6 NuGet 包 (EntityFramework) 安装，因为 EF Core 和 ef6 更高版本可以是在同一应用程序并行使用。</span><span class="sxs-lookup"><span data-stu-id="b5423-114">It is fine to leave the EF6 NuGet package (EntityFramework) installed, as EF Core and EF6 can be used side-by-side in the same application.</span></span> <span data-ttu-id="b5423-115">但是，如果你不打算在应用程序的任何区域使用 EF6，则卸载包将有助于在需要注意的代码段上生成编译错误。</span><span class="sxs-lookup"><span data-stu-id="b5423-115">However, if you aren't intending to use EF6 in any areas of your application, then uninstalling the package will help give compile errors on pieces of code that need attention.</span></span>

## <a name="update-your-code"></a><span data-ttu-id="b5423-116">更新代码</span><span class="sxs-lookup"><span data-stu-id="b5423-116">Update your code</span></span>

<span data-ttu-id="b5423-117">此时，它是一个寻址编译错误和查看代码，以查看你会影响从 EF6 和 EF Core 之间的行为更改。</span><span class="sxs-lookup"><span data-stu-id="b5423-117">At this point, it's a matter of addressing compilation errors and reviewing code to see if the behavior changes between EF6 and EF Core will impact you.</span></span>

## <a name="test-the-port"></a><span data-ttu-id="b5423-118">测试端口</span><span class="sxs-lookup"><span data-stu-id="b5423-118">Test the port</span></span>

<span data-ttu-id="b5423-119">只是因为你的应用程序将编译，并不意味着它成功移植到 EF Core。</span><span class="sxs-lookup"><span data-stu-id="b5423-119">Just because your application compiles, does not mean it is successfully ported to EF Core.</span></span> <span data-ttu-id="b5423-120">需要测试应用程序的所有区域，以确保不会对应用程序产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="b5423-120">You will need to test all areas of your application to ensure that none of the behavior changes have adversely impacted your application.</span></span>
