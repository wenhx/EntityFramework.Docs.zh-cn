---
title: 从 EF6 迁移到 EF Core-移植基于代码的模型-EF
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 2dce1a50-7d84-4856-abf6-2763dd9be99d
uid: efcore-and-ef6/porting/port-code
ms.openlocfilehash: 0a99eac2091c07d8bcf7d4e5e4bdc2afcaeee810
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181217"
---
# <a name="porting-an-ef6-code-based-model-to-ef-core"></a><span data-ttu-id="a494e-102">将基于 EF6 代码的模型移植到 EF Core</span><span class="sxs-lookup"><span data-stu-id="a494e-102">Porting an EF6 Code-Based Model to EF Core</span></span>

<span data-ttu-id="a494e-103">如果你已经阅读了所有注意事项，并且已准备好进行移植，则可以通过以下指南来帮助你入门。</span><span class="sxs-lookup"><span data-stu-id="a494e-103">If you've read all the caveats and you are ready to port, then here are some guidelines to help you get started.</span></span>

## <a name="install-ef-core-nuget-packages"></a><span data-ttu-id="a494e-104">安装 EF Core NuGet 包</span><span class="sxs-lookup"><span data-stu-id="a494e-104">Install EF Core NuGet packages</span></span>

<span data-ttu-id="a494e-105">若要使用 EF Core，请为要使用的数据库提供程序安装 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="a494e-105">To use EF Core, you install the NuGet package for the database provider you want to use.</span></span> <span data-ttu-id="a494e-106">例如，如果以 SQL Server 为目标，则将安装 `Microsoft.EntityFrameworkCore.SqlServer`。</span><span class="sxs-lookup"><span data-stu-id="a494e-106">For example, when targeting SQL Server, you would install `Microsoft.EntityFrameworkCore.SqlServer`.</span></span> <span data-ttu-id="a494e-107">有关详细信息，请参阅[数据库提供程序](../../core/providers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="a494e-107">See [Database Providers](../../core/providers/index.md) for details.</span></span>

<span data-ttu-id="a494e-108">如果计划使用迁移，则还应安装 `Microsoft.EntityFrameworkCore.Tools` 包。</span><span class="sxs-lookup"><span data-stu-id="a494e-108">If you are planning to use migrations, then you should also install the `Microsoft.EntityFrameworkCore.Tools` package.</span></span>

<span data-ttu-id="a494e-109">最好将从 EF6 NuGet 包 (EntityFramework) 安装，因为 EF Core 和 ef6 更高版本可以是在同一应用程序并行使用。</span><span class="sxs-lookup"><span data-stu-id="a494e-109">It is fine to leave the EF6 NuGet package (EntityFramework) installed, as EF Core and EF6 can be used side-by-side in the same application.</span></span> <span data-ttu-id="a494e-110">但是，如果你不打算在应用程序的任何区域使用 EF6，则卸载包将有助于在需要注意的代码段上生成编译错误。</span><span class="sxs-lookup"><span data-stu-id="a494e-110">However, if you aren't intending to use EF6 in any areas of your application, then uninstalling the package will help give compile errors on pieces of code that need attention.</span></span>

## <a name="swap-namespaces"></a><span data-ttu-id="a494e-111">交换命名空间</span><span class="sxs-lookup"><span data-stu-id="a494e-111">Swap namespaces</span></span>

<span data-ttu-id="a494e-112">在 EF6 中使用的大多数 Api 位于 `System.Data.Entity` 命名空间（以及相关的子命名空间）中。</span><span class="sxs-lookup"><span data-stu-id="a494e-112">Most APIs that you use in EF6 are in the `System.Data.Entity` namespace (and related sub-namespaces).</span></span> <span data-ttu-id="a494e-113">第一次代码更改是交换到 `Microsoft.EntityFrameworkCore` 命名空间。</span><span class="sxs-lookup"><span data-stu-id="a494e-113">The first code change is to swap to the `Microsoft.EntityFrameworkCore` namespace.</span></span> <span data-ttu-id="a494e-114">通常，您将从派生的上下文代码文件开始，然后从那里开始处理编译错误。</span><span class="sxs-lookup"><span data-stu-id="a494e-114">You would typically start with your derived context code file and then work out from there, addressing compilation errors as they occur.</span></span>

## <a name="context-configuration-connection-etc"></a><span data-ttu-id="a494e-115">上下文配置（连接等）</span><span class="sxs-lookup"><span data-stu-id="a494e-115">Context configuration (connection etc.)</span></span>

<span data-ttu-id="a494e-116">如[确保 EF Core 适用于你的应用程序](ensure-requirements.md)，EF Core 对检测要连接到的数据库的神奇程度较低。</span><span class="sxs-lookup"><span data-stu-id="a494e-116">As described in [Ensure EF Core Will Work for Your Application](ensure-requirements.md), EF Core has less magic around detecting the database to connect to.</span></span> <span data-ttu-id="a494e-117">你将需要覆盖派生上下文上的 `OnConfiguring` 方法，并使用数据库提供程序特定的 API 来设置与数据库的连接。</span><span class="sxs-lookup"><span data-stu-id="a494e-117">You will need to override the `OnConfiguring` method on your derived context, and use the database provider specific API to setup the connection to the database.</span></span>

<span data-ttu-id="a494e-118">大多数 EF6 应用程序将连接字符串存储在应用程序 `App/Web.config` 文件中。</span><span class="sxs-lookup"><span data-stu-id="a494e-118">Most EF6 applications store the connection string in the applications `App/Web.config` file.</span></span> <span data-ttu-id="a494e-119">在 EF Core 中，你将使用 `ConfigurationManager` API 读取此连接字符串。</span><span class="sxs-lookup"><span data-stu-id="a494e-119">In EF Core, you read this connection string using the `ConfigurationManager` API.</span></span> <span data-ttu-id="a494e-120">可能需要添加对 `System.Configuration` framework 程序集的引用才能使用此 API。</span><span class="sxs-lookup"><span data-stu-id="a494e-120">You may need to add a reference to the `System.Configuration` framework assembly to be able to use this API.</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
      optionsBuilder.UseSqlServer(ConfigurationManager.ConnectionStrings["BloggingDatabase"].ConnectionString);
    }
}
```

## <a name="update-your-code"></a><span data-ttu-id="a494e-121">更新代码</span><span class="sxs-lookup"><span data-stu-id="a494e-121">Update your code</span></span>

<span data-ttu-id="a494e-122">此时，需要解决编译错误和检查代码，以查看行为更改是否会影响你。</span><span class="sxs-lookup"><span data-stu-id="a494e-122">At this point, it's a matter of addressing compilation errors and reviewing code to see if the behavior changes will impact you.</span></span>

## <a name="existing-migrations"></a><span data-ttu-id="a494e-123">现有迁移</span><span class="sxs-lookup"><span data-stu-id="a494e-123">Existing migrations</span></span>

<span data-ttu-id="a494e-124">目前没有一种可行的方法可以将现有的 EF6 迁移移植到 EF Core。</span><span class="sxs-lookup"><span data-stu-id="a494e-124">There isn't really a feasible way to port existing EF6 migrations to EF Core.</span></span>

<span data-ttu-id="a494e-125">如果可能，最好假设已将 EF6 中的所有以前的迁移应用到数据库，然后使用 EF Core 开始从该点迁移架构。</span><span class="sxs-lookup"><span data-stu-id="a494e-125">If possible, it is best to assume that all previous migrations from EF6 have been applied to the database and then start migrating the schema from that point using EF Core.</span></span> <span data-ttu-id="a494e-126">若要执行此操作，可以使用 `Add-Migration` 命令在模型移植到 EF Core 后添加迁移。</span><span class="sxs-lookup"><span data-stu-id="a494e-126">To do this, you would use the `Add-Migration` command to add a migration once the model is ported to EF Core.</span></span> <span data-ttu-id="a494e-127">然后，将从基架迁移的 `Up` 和 `Down` 方法中删除所有代码。</span><span class="sxs-lookup"><span data-stu-id="a494e-127">You would then remove all code from the `Up` and `Down` methods of the scaffolded migration.</span></span> <span data-ttu-id="a494e-128">当基架初始迁移时，后续迁移将与模型进行比较。</span><span class="sxs-lookup"><span data-stu-id="a494e-128">Subsequent migrations will compare to the model when that initial migration was scaffolded.</span></span>

## <a name="test-the-port"></a><span data-ttu-id="a494e-129">测试端口</span><span class="sxs-lookup"><span data-stu-id="a494e-129">Test the port</span></span>

<span data-ttu-id="a494e-130">只是因为你的应用程序将编译，并不意味着它成功移植到 EF Core。</span><span class="sxs-lookup"><span data-stu-id="a494e-130">Just because your application compiles, does not mean it is successfully ported to EF Core.</span></span> <span data-ttu-id="a494e-131">需要测试应用程序的所有区域，以确保不会对应用程序产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="a494e-131">You will need to test all areas of your application to ensure that none of the behavior changes have adversely impacted your application.</span></span>
