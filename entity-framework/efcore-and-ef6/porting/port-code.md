---
title: 从 EF6 移植到 EF Core - 移植基于代码的模型 - EF
description: 关于将基于 Entity Framework 6 代码的模型应用程序移植到 Entity Framework Core 的特定信息
author: rowanmiller
ms.date: 10/27/2016
uid: efcore-and-ef6/porting/port-code
ms.openlocfilehash: a5bbdc2ee95ea6bea96e24bee4588b524e0ffc58
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073573"
---
# <a name="porting-an-ef6-code-based-model-to-ef-core"></a><span data-ttu-id="79c7f-103">将基于 EF6 代码的模型移植到 EF Core</span><span class="sxs-lookup"><span data-stu-id="79c7f-103">Porting an EF6 Code-Based Model to EF Core</span></span>

<span data-ttu-id="79c7f-104">如果你已阅读所有说明且已准备好进行移植，请查看下面一些指南，它们可帮你入门。</span><span class="sxs-lookup"><span data-stu-id="79c7f-104">If you've read all the caveats and you are ready to port, then here are some guidelines to help you get started.</span></span>

## <a name="install-ef-core-nuget-packages"></a><span data-ttu-id="79c7f-105">安装 EF Core NuGet 包</span><span class="sxs-lookup"><span data-stu-id="79c7f-105">Install EF Core NuGet packages</span></span>

<span data-ttu-id="79c7f-106">要使用 EF Core，请针对要使用的数据库提供程序安装 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="79c7f-106">To use EF Core, you install the NuGet package for the database provider you want to use.</span></span> <span data-ttu-id="79c7f-107">例如，如果以 SQL Server 为目标，则安装 `Microsoft.EntityFrameworkCore.SqlServer`。</span><span class="sxs-lookup"><span data-stu-id="79c7f-107">For example, when targeting SQL Server, you would install `Microsoft.EntityFrameworkCore.SqlServer`.</span></span> <span data-ttu-id="79c7f-108">有关详细信息，请参阅[数据库提供程序](xref:core/providers/index)。</span><span class="sxs-lookup"><span data-stu-id="79c7f-108">See [Database Providers](xref:core/providers/index) for details.</span></span>

<span data-ttu-id="79c7f-109">如果计划使用迁移，则还应安装 `Microsoft.EntityFrameworkCore.Tools` 包。</span><span class="sxs-lookup"><span data-stu-id="79c7f-109">If you are planning to use migrations, then you should also install the `Microsoft.EntityFrameworkCore.Tools` package.</span></span>

<span data-ttu-id="79c7f-110">可将 EF6 NuGet 包 (EntityFramework) 保留在安装状态，因为 EF Core 和 EF6 可在同一应用程序中并排使用。</span><span class="sxs-lookup"><span data-stu-id="79c7f-110">It is fine to leave the EF6 NuGet package (EntityFramework) installed, as EF Core and EF6 can be used side-by-side in the same application.</span></span> <span data-ttu-id="79c7f-111">但是，如果你不打算在应用程序的任何区域使用 EF6，则卸载此包将有助于就需注意的代码片段生成编译错误。</span><span class="sxs-lookup"><span data-stu-id="79c7f-111">However, if you aren't intending to use EF6 in any areas of your application, then uninstalling the package will help give compile errors on pieces of code that need attention.</span></span>

## <a name="swap-namespaces"></a><span data-ttu-id="79c7f-112">交换命名空间</span><span class="sxs-lookup"><span data-stu-id="79c7f-112">Swap namespaces</span></span>

<span data-ttu-id="79c7f-113">在 EF6 中使用的大多数 API 位于 `System.Data.Entity` 命名空间（及相关的子命名空间）中。</span><span class="sxs-lookup"><span data-stu-id="79c7f-113">Most APIs that you use in EF6 are in the `System.Data.Entity` namespace (and related sub-namespaces).</span></span> <span data-ttu-id="79c7f-114">第一个代码更改是交换到 `Microsoft.EntityFrameworkCore` 命名空间。</span><span class="sxs-lookup"><span data-stu-id="79c7f-114">The first code change is to swap to the `Microsoft.EntityFrameworkCore` namespace.</span></span> <span data-ttu-id="79c7f-115">通常，你首先是处理已派生的上下文代码文件，然后从此处开始，在出现编译错误时处理这些错误。</span><span class="sxs-lookup"><span data-stu-id="79c7f-115">You would typically start with your derived context code file and then work out from there, addressing compilation errors as they occur.</span></span>

## <a name="context-configuration-connection-etc"></a><span data-ttu-id="79c7f-116">上下文配置（连接等）</span><span class="sxs-lookup"><span data-stu-id="79c7f-116">Context configuration (connection etc.)</span></span>

<span data-ttu-id="79c7f-117">如[确保 EF Core 适用于你的应用程序](xref:efcore-and-ef6/porting/index)中所述，EF Core 检测到连接到的数据库时操作更一目了然。</span><span class="sxs-lookup"><span data-stu-id="79c7f-117">As described in [Ensure EF Core Will Work for Your Application](xref:efcore-and-ef6/porting/index), EF Core has less magic around detecting the database to connect to.</span></span> <span data-ttu-id="79c7f-118">你将需要替代已派生的上下文中的 `OnConfiguring` 方法，并使用数据库提供程序特定的 API 设置到数据库的连接。</span><span class="sxs-lookup"><span data-stu-id="79c7f-118">You will need to override the `OnConfiguring` method on your derived context, and use the database provider specific API to setup the connection to the database.</span></span>

<span data-ttu-id="79c7f-119">大多数 EF6 应用程序将连接字符串存储在应用程序 `App/Web.config` 文件中。</span><span class="sxs-lookup"><span data-stu-id="79c7f-119">Most EF6 applications store the connection string in the applications `App/Web.config` file.</span></span> <span data-ttu-id="79c7f-120">在 EF Core 中，你将使用 `ConfigurationManager` API 读取此连接字符串。</span><span class="sxs-lookup"><span data-stu-id="79c7f-120">In EF Core, you read this connection string using the `ConfigurationManager` API.</span></span> <span data-ttu-id="79c7f-121">你可能还需要添加对 `System.Configuration` 框架程序集的引用才能使用此 API。</span><span class="sxs-lookup"><span data-stu-id="79c7f-121">You may need to add a reference to the `System.Configuration` framework assembly to be able to use this API.</span></span>

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

## <a name="update-your-code"></a><span data-ttu-id="79c7f-122">更新代码</span><span class="sxs-lookup"><span data-stu-id="79c7f-122">Update your code</span></span>

<span data-ttu-id="79c7f-123">此时，需要处理编译错误并检查代码，以查看行为变化是否会对你产生影响。</span><span class="sxs-lookup"><span data-stu-id="79c7f-123">At this point, it's a matter of addressing compilation errors and reviewing code to see if the behavior changes will impact you.</span></span>

## <a name="existing-migrations"></a><span data-ttu-id="79c7f-124">现有迁移</span><span class="sxs-lookup"><span data-stu-id="79c7f-124">Existing migrations</span></span>

<span data-ttu-id="79c7f-125">目前没有一种可行的方法可将现有 EF6 迁移移植到 EF Core。</span><span class="sxs-lookup"><span data-stu-id="79c7f-125">There isn't really a feasible way to port existing EF6 migrations to EF Core.</span></span>

<span data-ttu-id="79c7f-126">如果可能，最好假设之前来自 EF6 的所有迁移都已应用到数据库，然后再使用 EF Core 从此处开始迁移架构。</span><span class="sxs-lookup"><span data-stu-id="79c7f-126">If possible, it is best to assume that all previous migrations from EF6 have been applied to the database and then start migrating the schema from that point using EF Core.</span></span> <span data-ttu-id="79c7f-127">为此，一旦模型移植到 EF Core，即可使用 `Add-Migration` 添加迁移。</span><span class="sxs-lookup"><span data-stu-id="79c7f-127">To do this, you would use the `Add-Migration` command to add a migration once the model is ported to EF Core.</span></span> <span data-ttu-id="79c7f-128">然后，需已设置基架的迁移的 `Up` 和 `Down` 方法中删除所有代码。</span><span class="sxs-lookup"><span data-stu-id="79c7f-128">You would then remove all code from the `Up` and `Down` methods of the scaffolded migration.</span></span> <span data-ttu-id="79c7f-129">设置初始迁移的基架后，后续迁移将与此模型进行比较。</span><span class="sxs-lookup"><span data-stu-id="79c7f-129">Subsequent migrations will compare to the model when that initial migration was scaffolded.</span></span>

## <a name="test-the-port"></a><span data-ttu-id="79c7f-130">测试移植</span><span class="sxs-lookup"><span data-stu-id="79c7f-130">Test the port</span></span>

<span data-ttu-id="79c7f-131">你的应用程序进行了编译，并不意味着它会成功移植到 EF Core。</span><span class="sxs-lookup"><span data-stu-id="79c7f-131">Just because your application compiles, does not mean it is successfully ported to EF Core.</span></span> <span data-ttu-id="79c7f-132">需要测试应用程序的所有区域，确保行为变化都未对你的应用程序产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="79c7f-132">You will need to test all areas of your application to ensure that none of the behavior changes have adversely impacted your application.</span></span>
