---
title: 支持的 .NET 实现 - EF Core
author: bricelam
ms.date: 03/03/2020
uid: core/platforms/index
ms.openlocfilehash: 2738645596a325a1a43fd5b2164e370dd8c5893e
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434118"
---
# <a name="net-implementations-supported-by-ef-core"></a><span data-ttu-id="5e28d-102">EF Core 支持的 .NET 实现</span><span class="sxs-lookup"><span data-stu-id="5e28d-102">.NET implementations supported by EF Core</span></span>

<span data-ttu-id="5e28d-103">我们希望 EF Core 可供开发人员在所有新式 .NET 实现上使用，并且我们仍在努力实现这一目标。</span><span class="sxs-lookup"><span data-stu-id="5e28d-103">We want EF Core to be available to developers on all modern .NET implementations, and we're still working towards that goal.</span></span> <span data-ttu-id="5e28d-104">尽管自动测试证明 EF Core 支持 .NET Core，同时许多应用程序已成功使用 EF Core，但 Mono、Xamarin 和 UWP 仍存在一些问题。</span><span class="sxs-lookup"><span data-stu-id="5e28d-104">While EF Core's support on .NET Core is covered by automated testing and many applications known to be using it successfully, Mono, Xamarin and UWP have some issues.</span></span>

## <a name="overview"></a><span data-ttu-id="5e28d-105">概述</span><span class="sxs-lookup"><span data-stu-id="5e28d-105">Overview</span></span>

<span data-ttu-id="5e28d-106">下表提供了每个 .NET 实现的指南：</span><span class="sxs-lookup"><span data-stu-id="5e28d-106">The following table provides guidance for each .NET implementation:</span></span>

| <span data-ttu-id="5e28d-107">EF Core</span><span class="sxs-lookup"><span data-stu-id="5e28d-107">EF Core</span></span>                       | <span data-ttu-id="5e28d-108">2.1 和 3.1</span><span class="sxs-lookup"><span data-stu-id="5e28d-108">2.1 and 3.1</span></span> |
|:------------------------------|:------------|
| <span data-ttu-id="5e28d-109">.NET Standard</span><span class="sxs-lookup"><span data-stu-id="5e28d-109">.NET Standard</span></span>                 | <span data-ttu-id="5e28d-110">2.0</span><span class="sxs-lookup"><span data-stu-id="5e28d-110">2.0</span></span>         |
| <span data-ttu-id="5e28d-111">.NET Core</span><span class="sxs-lookup"><span data-stu-id="5e28d-111">.NET Core</span></span>                     | <span data-ttu-id="5e28d-112">2.0</span><span class="sxs-lookup"><span data-stu-id="5e28d-112">2.0</span></span>         |
| <span data-ttu-id="5e28d-113">.NET Framework<sup>(1)</sup></span><span class="sxs-lookup"><span data-stu-id="5e28d-113">.NET Framework<sup>(1)</sup></span></span>  | <span data-ttu-id="5e28d-114">4.7.2</span><span class="sxs-lookup"><span data-stu-id="5e28d-114">4.7.2</span></span>       |
| <span data-ttu-id="5e28d-115">Mono</span><span class="sxs-lookup"><span data-stu-id="5e28d-115">Mono</span></span>                          | <span data-ttu-id="5e28d-116">5.4</span><span class="sxs-lookup"><span data-stu-id="5e28d-116">5.4</span></span>         |
| <span data-ttu-id="5e28d-117">Xamarin.iOS<sup>(2)</sup></span><span class="sxs-lookup"><span data-stu-id="5e28d-117">Xamarin.iOS<sup>(2)</sup></span></span>     | <span data-ttu-id="5e28d-118">10.14</span><span class="sxs-lookup"><span data-stu-id="5e28d-118">10.14</span></span>       |
| <span data-ttu-id="5e28d-119">Xamarin.Android<sup>(2)</sup></span><span class="sxs-lookup"><span data-stu-id="5e28d-119">Xamarin.Android<sup>(2)</sup></span></span> | <span data-ttu-id="5e28d-120">8.0</span><span class="sxs-lookup"><span data-stu-id="5e28d-120">8.0</span></span>         |
| <span data-ttu-id="5e28d-121">UWP<sup>(3)</sup></span><span class="sxs-lookup"><span data-stu-id="5e28d-121">UWP<sup>(3)</sup></span></span>             | <span data-ttu-id="5e28d-122">10.0.16299</span><span class="sxs-lookup"><span data-stu-id="5e28d-122">10.0.16299</span></span>  |
| <span data-ttu-id="5e28d-123">Unity<sup>(4)</sup></span><span class="sxs-lookup"><span data-stu-id="5e28d-123">Unity<sup>(4)</sup></span></span>           | <span data-ttu-id="5e28d-124">2018 年 1 月</span><span class="sxs-lookup"><span data-stu-id="5e28d-124">2018.1</span></span>      |

<span data-ttu-id="5e28d-125"><sup>(1)</sup> 请参阅下面的 [.NET Framework](#net-framework) 部分。</span><span class="sxs-lookup"><span data-stu-id="5e28d-125"><sup>(1)</sup> See the [.NET Framework](#net-framework) section below.</span></span>

<span data-ttu-id="5e28d-126"><sup>(2)</sup> Xamarin 存在一些问题和已知限制，这些问题和限制可能会阻止部分使用 EF Core 开发的应用程序正常运行。</span><span class="sxs-lookup"><span data-stu-id="5e28d-126"><sup>(2)</sup> There are issues and known limitations with Xamarin which may prevent some applications developed using EF Core from working correctly.</span></span> <span data-ttu-id="5e28d-127">查看[未解决问题](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-xamarin)列表，了解解决方法。</span><span class="sxs-lookup"><span data-stu-id="5e28d-127">Check the list of [active issues](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-xamarin) for workarounds.</span></span>

<span data-ttu-id="5e28d-128"><sup>(3)</sup> 建议使用 EF Core 2.0.1 和更高版本。</span><span class="sxs-lookup"><span data-stu-id="5e28d-128"><sup>(3)</sup> EF Core 2.0.1 and newer recommended.</span></span> <span data-ttu-id="5e28d-129">安装 [.NET Core UWP 6.x 包](https://www.nuget.org/packages/Microsoft.NETCore.UniversalWindowsPlatform/)。</span><span class="sxs-lookup"><span data-stu-id="5e28d-129">Install the [.NET Core UWP 6.x package](https://www.nuget.org/packages/Microsoft.NETCore.UniversalWindowsPlatform/).</span></span> <span data-ttu-id="5e28d-130">请参阅本文的[通用 Windows 平台](#universal-windows-platform)部分。</span><span class="sxs-lookup"><span data-stu-id="5e28d-130">See the [Universal Windows Platform](#universal-windows-platform) section of this article.</span></span>

<span data-ttu-id="5e28d-131"><sup>(4)</sup> Unity 存在问题和已知限制。</span><span class="sxs-lookup"><span data-stu-id="5e28d-131"><sup>(4)</sup> There are issues and known limitations with Unity.</span></span> <span data-ttu-id="5e28d-132">查看[未解决问题](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-unity)列表。</span><span class="sxs-lookup"><span data-stu-id="5e28d-132">Check the list of [active issues](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-unity).</span></span>

## <a name="net-framework"></a><span data-ttu-id="5e28d-133">.NET Framework</span><span class="sxs-lookup"><span data-stu-id="5e28d-133">.NET Framework</span></span>

<span data-ttu-id="5e28d-134">面向 .NET Framework 的应用程序可能需要更改为使用 .NET Standard 库：</span><span class="sxs-lookup"><span data-stu-id="5e28d-134">Applications that target .NET Framework may need changes to work with .NET Standard libraries:</span></span>

<span data-ttu-id="5e28d-135">编辑项目文件，并确保以下条目出现在初始属性组中：</span><span class="sxs-lookup"><span data-stu-id="5e28d-135">Edit the project file and make sure the following entry appears in the initial property group:</span></span>

``` xml
<AutoGenerateBindingRedirects>true</AutoGenerateBindingRedirects>
```

<span data-ttu-id="5e28d-136">对于测试项目，还要确保存在以下条目：</span><span class="sxs-lookup"><span data-stu-id="5e28d-136">For test projects, also make sure the following entry is present:</span></span>

``` xml
<GenerateBindingRedirectsOutputType>true</GenerateBindingRedirectsOutputType>
```

<span data-ttu-id="5e28d-137">如果想要使用较旧版本的 Visual Studio，请确保[将 NuGet 客户端升级到版本 3.6.0](https://www.nuget.org/downloads)，以便使用 .NET Standard 2.0 库。</span><span class="sxs-lookup"><span data-stu-id="5e28d-137">If you want to use an older version of Visual Studio, make sure you [upgrade the NuGet client to version 3.6.0](https://www.nuget.org/downloads) to work with .NET Standard 2.0 libraries.</span></span>

<span data-ttu-id="5e28d-138">如果可能，我们还建议从 NuGet packages.config 迁移到 PackageReference。</span><span class="sxs-lookup"><span data-stu-id="5e28d-138">We also recommend migrating from NuGet packages.config to PackageReference if possible.</span></span> <span data-ttu-id="5e28d-139">请将以下属性添加到项目文件：</span><span class="sxs-lookup"><span data-stu-id="5e28d-139">Add the following property to your project file:</span></span>

``` xml
<RestoreProjectStyle>PackageReference</RestoreProjectStyle>
```

## <a name="universal-windows-platform"></a><span data-ttu-id="5e28d-140">通用 Windows 平台</span><span class="sxs-lookup"><span data-stu-id="5e28d-140">Universal Windows Platform</span></span>

<span data-ttu-id="5e28d-141">早期的 EF Core 和 .NET UWP 版本存在许多兼容性问题，尤其是用于使用 .NET Native 工具链编译的应用程序时。</span><span class="sxs-lookup"><span data-stu-id="5e28d-141">Earlier versions of EF Core and .NET UWP had numerous compatibility issues, especially with applications compiled with the .NET Native toolchain.</span></span> <span data-ttu-id="5e28d-142">新的 .NET UWP 版本增加了对 .NET Standard 2.0 的支持，且包含了 .NET Native 2.0，修复了之前报告的大多数兼容性问题。</span><span class="sxs-lookup"><span data-stu-id="5e28d-142">The new .NET UWP version adds support for .NET Standard 2.0 and contains .NET Native 2.0, which fixes most of the compatibility issues previously reported.</span></span> <span data-ttu-id="5e28d-143">我们使用 UWP 对 EF Core 2.0.1 进行了更彻底的测试，但测试不是自动执行的。</span><span class="sxs-lookup"><span data-stu-id="5e28d-143">EF Core 2.0.1 has been tested more thoroughly with UWP but testing is not automated.</span></span>

<span data-ttu-id="5e28d-144">在 UWP 上使用 EF Core 时：</span><span class="sxs-lookup"><span data-stu-id="5e28d-144">When using EF Core on UWP:</span></span>

* <span data-ttu-id="5e28d-145">若要优化查询性能，请避免在 LINQ 查询中使用匿名类型。</span><span class="sxs-lookup"><span data-stu-id="5e28d-145">To optimize query performance, avoid anonymous types in LINQ queries.</span></span> <span data-ttu-id="5e28d-146">将 UWP 应用程序部署到应用商店要求使用 .NET Native 编译应用程序。</span><span class="sxs-lookup"><span data-stu-id="5e28d-146">Deploying a UWP application to the app store requires an application to be compiled with .NET Native.</span></span> <span data-ttu-id="5e28d-147">使用匿名类型的查询在 .NET Native 上性能较差。</span><span class="sxs-lookup"><span data-stu-id="5e28d-147">Queries with anonymous types have worse performance on .NET Native.</span></span>

* <span data-ttu-id="5e28d-148">若要优化 `SaveChanges()` 性能，请使用 [ChangeTrackingStrategy.ChangingAndChangedNotifications](/dotnet/api/microsoft.entityframeworkcore.changetrackingstrategy)，并在你的实体类型中实施 [INotifyPropertyChanged](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanged.aspx)、[INotifyPropertyChanging](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanging.aspx) 和 [INotifyCollectionChanged](https://msdn.microsoft.com/library/system.collections.specialized.inotifycollectionchanged.aspx)。</span><span class="sxs-lookup"><span data-stu-id="5e28d-148">To optimize `SaveChanges()` performance, use [ChangeTrackingStrategy.ChangingAndChangedNotifications](/dotnet/api/microsoft.entityframeworkcore.changetrackingstrategy) and implement [INotifyPropertyChanged](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanged.aspx), [INotifyPropertyChanging](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanging.aspx), and [INotifyCollectionChanged](https://msdn.microsoft.com/library/system.collections.specialized.inotifycollectionchanged.aspx) in your entity types.</span></span>

## <a name="report-issues"></a><span data-ttu-id="5e28d-149">报告问题</span><span class="sxs-lookup"><span data-stu-id="5e28d-149">Report issues</span></span>

<span data-ttu-id="5e28d-150">对于未按预期工作的任意组合，我们建议在 [EF Core 问题跟踪程序](https://github.com/aspnet/entityframeworkcore/issues/new)中创建新问题。</span><span class="sxs-lookup"><span data-stu-id="5e28d-150">For any combination that doesn't work as expected, we encourage creating new issues on the [EF Core issue tracker](https://github.com/aspnet/entityframeworkcore/issues/new).</span></span> <span data-ttu-id="5e28d-151">对于特定于 Xamarin 的问题，请使用 [Xamarin.Android](https://github.com/xamarin/xamarin-android/issues/new) 或 [Xamarin.iOS](https://github.com/xamarin/xamarin-macios/issues/new) 问题跟踪程序。</span><span class="sxs-lookup"><span data-stu-id="5e28d-151">For Xamarin-specific issues use the issue tracker for [Xamarin.Android](https://github.com/xamarin/xamarin-android/issues/new) or [Xamarin.iOS](https://github.com/xamarin/xamarin-macios/issues/new).</span></span>
