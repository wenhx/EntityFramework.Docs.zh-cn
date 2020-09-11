---
title: 支持的 .NET 实现 - EF Core
description: 有关跨 Entity Framework Core 版本的支持平台的信息
author: bricelam
ms.date: 06/26/2020
uid: core/platforms/index
ms.openlocfilehash: f57002af869c16eeedf931085f5e5ff9e72e2984
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616657"
---
# <a name="net-implementations-supported-by-ef-core"></a><span data-ttu-id="41c40-103">EF Core 支持的 .NET 实现</span><span class="sxs-lookup"><span data-stu-id="41c40-103">.NET implementations supported by EF Core</span></span>

<span data-ttu-id="41c40-104">我们希望 EF Core 可供开发人员在所有新式 .NET 实现上使用，并且我们仍在努力实现这一目标。</span><span class="sxs-lookup"><span data-stu-id="41c40-104">We want EF Core to be available to developers on all modern .NET implementations, and we're still working towards that goal.</span></span> <span data-ttu-id="41c40-105">尽管自动测试证明 EF Core 支持 .NET Core，同时许多应用程序已成功使用 EF Core，但 Mono、Xamarin 和 UWP 仍存在一些问题。</span><span class="sxs-lookup"><span data-stu-id="41c40-105">While EF Core's support on .NET Core is covered by automated testing and many applications known to be using it successfully, Mono, Xamarin and UWP have some issues.</span></span>

## <a name="overview"></a><span data-ttu-id="41c40-106">概述</span><span class="sxs-lookup"><span data-stu-id="41c40-106">Overview</span></span>

<span data-ttu-id="41c40-107">下表提供了每个 .NET 实现的指南：</span><span class="sxs-lookup"><span data-stu-id="41c40-107">The following table provides guidance for each .NET implementation:</span></span>

| <span data-ttu-id="41c40-108">EF Core</span><span class="sxs-lookup"><span data-stu-id="41c40-108">EF Core</span></span>                       | <span data-ttu-id="41c40-109">2.1 和 3.1</span><span class="sxs-lookup"><span data-stu-id="41c40-109">2.1 and 3.1</span></span> | <span data-ttu-id="41c40-110">5.0</span><span class="sxs-lookup"><span data-stu-id="41c40-110">5.0</span></span>             |
|:------------------------------|:------------|:----------------|
| <span data-ttu-id="41c40-111">.NET Standard</span><span class="sxs-lookup"><span data-stu-id="41c40-111">.NET Standard</span></span>                 | <span data-ttu-id="41c40-112">2.0</span><span class="sxs-lookup"><span data-stu-id="41c40-112">2.0</span></span>         | <span data-ttu-id="41c40-113">2.1</span><span class="sxs-lookup"><span data-stu-id="41c40-113">2.1</span></span>             |
| <span data-ttu-id="41c40-114">.NET Core</span><span class="sxs-lookup"><span data-stu-id="41c40-114">.NET Core</span></span>                     | <span data-ttu-id="41c40-115">2.0</span><span class="sxs-lookup"><span data-stu-id="41c40-115">2.0</span></span>         | <span data-ttu-id="41c40-116">3.0</span><span class="sxs-lookup"><span data-stu-id="41c40-116">3.0</span></span>             |
| <span data-ttu-id="41c40-117">.NET Framework<sup>(1)</sup></span><span class="sxs-lookup"><span data-stu-id="41c40-117">.NET Framework<sup>(1)</sup></span></span>  | <span data-ttu-id="41c40-118">4.7.2</span><span class="sxs-lookup"><span data-stu-id="41c40-118">4.7.2</span></span>       | <span data-ttu-id="41c40-119">（不支持）</span><span class="sxs-lookup"><span data-stu-id="41c40-119">(not supported)</span></span> |
| <span data-ttu-id="41c40-120">Mono</span><span class="sxs-lookup"><span data-stu-id="41c40-120">Mono</span></span>                          | <span data-ttu-id="41c40-121">5.4</span><span class="sxs-lookup"><span data-stu-id="41c40-121">5.4</span></span>         | <span data-ttu-id="41c40-122">6.4</span><span class="sxs-lookup"><span data-stu-id="41c40-122">6.4</span></span>             |
| <span data-ttu-id="41c40-123">Xamarin.iOS<sup>(2)</sup></span><span class="sxs-lookup"><span data-stu-id="41c40-123">Xamarin.iOS<sup>(2)</sup></span></span>     | <span data-ttu-id="41c40-124">10.14</span><span class="sxs-lookup"><span data-stu-id="41c40-124">10.14</span></span>       | <span data-ttu-id="41c40-125">12.16</span><span class="sxs-lookup"><span data-stu-id="41c40-125">12.16</span></span>           |
| <span data-ttu-id="41c40-126">Xamarin.Mac<sup>(2)</sup></span><span class="sxs-lookup"><span data-stu-id="41c40-126">Xamarin.Mac<sup>(2)</sup></span></span>     | <span data-ttu-id="41c40-127">3.8</span><span class="sxs-lookup"><span data-stu-id="41c40-127">3.8</span></span>         | <span data-ttu-id="41c40-128">5.16</span><span class="sxs-lookup"><span data-stu-id="41c40-128">5.16</span></span>            |
| <span data-ttu-id="41c40-129">Xamarin.Android<sup>(2)</sup></span><span class="sxs-lookup"><span data-stu-id="41c40-129">Xamarin.Android<sup>(2)</sup></span></span> | <span data-ttu-id="41c40-130">8.0</span><span class="sxs-lookup"><span data-stu-id="41c40-130">8.0</span></span>         | <span data-ttu-id="41c40-131">10.0</span><span class="sxs-lookup"><span data-stu-id="41c40-131">10.0</span></span>            |
| <span data-ttu-id="41c40-132">UWP<sup>(3)</sup></span><span class="sxs-lookup"><span data-stu-id="41c40-132">UWP<sup>(3)</sup></span></span>             | <span data-ttu-id="41c40-133">10.0.16299</span><span class="sxs-lookup"><span data-stu-id="41c40-133">10.0.16299</span></span>  | <span data-ttu-id="41c40-134">待定</span><span class="sxs-lookup"><span data-stu-id="41c40-134">TBD</span></span>             |
| <span data-ttu-id="41c40-135">Unity<sup>(4)</sup></span><span class="sxs-lookup"><span data-stu-id="41c40-135">Unity<sup>(4)</sup></span></span>           | <span data-ttu-id="41c40-136">2018 年 1 月</span><span class="sxs-lookup"><span data-stu-id="41c40-136">2018.1</span></span>      | <span data-ttu-id="41c40-137">待定</span><span class="sxs-lookup"><span data-stu-id="41c40-137">TBD</span></span>             |

<span data-ttu-id="41c40-138"><sup>(1)</sup> 请参阅下面的 [.NET Framework](#net-framework) 部分。</span><span class="sxs-lookup"><span data-stu-id="41c40-138"><sup>(1)</sup> See the [.NET Framework](#net-framework) section below.</span></span>

<span data-ttu-id="41c40-139"><sup>(2)</sup> Xamarin 存在一些问题和已知限制，这些问题和限制可能会阻止部分使用 EF Core 开发的应用程序正常运行。</span><span class="sxs-lookup"><span data-stu-id="41c40-139"><sup>(2)</sup> There are issues and known limitations with Xamarin which may prevent some applications developed using EF Core from working correctly.</span></span> <span data-ttu-id="41c40-140">查看[未解决问题](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-xamarin)列表，了解解决方法。</span><span class="sxs-lookup"><span data-stu-id="41c40-140">Check the list of [active issues](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-xamarin) for workarounds.</span></span>

<span data-ttu-id="41c40-141"><sup>(3)</sup> 建议使用 EF Core 2.0.1 和更高版本。</span><span class="sxs-lookup"><span data-stu-id="41c40-141"><sup>(3)</sup> EF Core 2.0.1 and newer recommended.</span></span> <span data-ttu-id="41c40-142">安装 [.NET Core UWP 6.x 包](https://www.nuget.org/packages/Microsoft.NETCore.UniversalWindowsPlatform/)。</span><span class="sxs-lookup"><span data-stu-id="41c40-142">Install the [.NET Core UWP 6.x package](https://www.nuget.org/packages/Microsoft.NETCore.UniversalWindowsPlatform/).</span></span> <span data-ttu-id="41c40-143">请参阅本文的[通用 Windows 平台](#universal-windows-platform)部分。</span><span class="sxs-lookup"><span data-stu-id="41c40-143">See the [Universal Windows Platform](#universal-windows-platform) section of this article.</span></span>

<span data-ttu-id="41c40-144"><sup>(4)</sup> Unity 存在问题和已知限制。</span><span class="sxs-lookup"><span data-stu-id="41c40-144"><sup>(4)</sup> There are issues and known limitations with Unity.</span></span> <span data-ttu-id="41c40-145">查看[未解决问题](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-unity)列表。</span><span class="sxs-lookup"><span data-stu-id="41c40-145">Check the list of [active issues](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-unity).</span></span>

## <a name="net-framework"></a><span data-ttu-id="41c40-146">.NET Framework</span><span class="sxs-lookup"><span data-stu-id="41c40-146">.NET Framework</span></span>

<span data-ttu-id="41c40-147">面向 .NET Framework 的应用程序可能需要更改为使用 .NET Standard 库：</span><span class="sxs-lookup"><span data-stu-id="41c40-147">Applications that target .NET Framework may need changes to work with .NET Standard libraries:</span></span>

<span data-ttu-id="41c40-148">编辑项目文件，并确保以下条目出现在初始属性组中：</span><span class="sxs-lookup"><span data-stu-id="41c40-148">Edit the project file and make sure the following entry appears in the initial property group:</span></span>

``` xml
<AutoGenerateBindingRedirects>true</AutoGenerateBindingRedirects>
```

<span data-ttu-id="41c40-149">对于测试项目，还要确保存在以下条目：</span><span class="sxs-lookup"><span data-stu-id="41c40-149">For test projects, also make sure the following entry is present:</span></span>

``` xml
<GenerateBindingRedirectsOutputType>true</GenerateBindingRedirectsOutputType>
```

<span data-ttu-id="41c40-150">如果想要使用较旧版本的 Visual Studio，请确保[将 NuGet 客户端升级到版本 3.6.0](https://www.nuget.org/downloads)，以便使用 .NET Standard 2.0 库。</span><span class="sxs-lookup"><span data-stu-id="41c40-150">If you want to use an older version of Visual Studio, make sure you [upgrade the NuGet client to version 3.6.0](https://www.nuget.org/downloads) to work with .NET Standard 2.0 libraries.</span></span>

<span data-ttu-id="41c40-151">如果可能，我们还建议从 NuGet packages.config 迁移到 PackageReference。</span><span class="sxs-lookup"><span data-stu-id="41c40-151">We also recommend migrating from NuGet packages.config to PackageReference if possible.</span></span> <span data-ttu-id="41c40-152">请将以下属性添加到项目文件：</span><span class="sxs-lookup"><span data-stu-id="41c40-152">Add the following property to your project file:</span></span>

``` xml
<RestoreProjectStyle>PackageReference</RestoreProjectStyle>
```

## <a name="universal-windows-platform"></a><span data-ttu-id="41c40-153">通用 Windows 平台</span><span class="sxs-lookup"><span data-stu-id="41c40-153">Universal Windows Platform</span></span>

<span data-ttu-id="41c40-154">早期的 EF Core 和 .NET UWP 版本存在许多兼容性问题，尤其是用于使用 .NET Native 工具链编译的应用程序时。</span><span class="sxs-lookup"><span data-stu-id="41c40-154">Earlier versions of EF Core and .NET UWP had numerous compatibility issues, especially with applications compiled with the .NET Native toolchain.</span></span> <span data-ttu-id="41c40-155">新的 .NET UWP 版本增加了对 .NET Standard 2.0 的支持，且包含了 .NET Native 2.0，修复了之前报告的大多数兼容性问题。</span><span class="sxs-lookup"><span data-stu-id="41c40-155">The new .NET UWP version adds support for .NET Standard 2.0 and contains .NET Native 2.0, which fixes most of the compatibility issues previously reported.</span></span> <span data-ttu-id="41c40-156">我们使用 UWP 对 EF Core 2.0.1 进行了更彻底的测试，但测试不是自动执行的。</span><span class="sxs-lookup"><span data-stu-id="41c40-156">EF Core 2.0.1 has been tested more thoroughly with UWP but testing is not automated.</span></span>

<span data-ttu-id="41c40-157">在 UWP 上使用 EF Core 时：</span><span class="sxs-lookup"><span data-stu-id="41c40-157">When using EF Core on UWP:</span></span>

* <span data-ttu-id="41c40-158">若要优化查询性能，请避免在 LINQ 查询中使用匿名类型。</span><span class="sxs-lookup"><span data-stu-id="41c40-158">To optimize query performance, avoid anonymous types in LINQ queries.</span></span> <span data-ttu-id="41c40-159">将 UWP 应用程序部署到应用商店要求使用 .NET Native 编译应用程序。</span><span class="sxs-lookup"><span data-stu-id="41c40-159">Deploying a UWP application to the app store requires an application to be compiled with .NET Native.</span></span> <span data-ttu-id="41c40-160">使用匿名类型的查询在 .NET Native 上性能较差。</span><span class="sxs-lookup"><span data-stu-id="41c40-160">Queries with anonymous types have worse performance on .NET Native.</span></span>

* <span data-ttu-id="41c40-161">若要优化 `SaveChanges()` 性能，请使用 [ChangeTrackingStrategy.ChangingAndChangedNotifications](/dotnet/api/microsoft.entityframeworkcore.changetrackingstrategy)，并在你的实体类型中实施 [INotifyPropertyChanged](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanged.aspx)、[INotifyPropertyChanging](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanging.aspx) 和 [INotifyCollectionChanged](https://msdn.microsoft.com/library/system.collections.specialized.inotifycollectionchanged.aspx)。</span><span class="sxs-lookup"><span data-stu-id="41c40-161">To optimize `SaveChanges()` performance, use [ChangeTrackingStrategy.ChangingAndChangedNotifications](/dotnet/api/microsoft.entityframeworkcore.changetrackingstrategy) and implement [INotifyPropertyChanged](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanged.aspx), [INotifyPropertyChanging](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanging.aspx), and [INotifyCollectionChanged](https://msdn.microsoft.com/library/system.collections.specialized.inotifycollectionchanged.aspx) in your entity types.</span></span>

## <a name="report-issues"></a><span data-ttu-id="41c40-162">报告问题</span><span class="sxs-lookup"><span data-stu-id="41c40-162">Report issues</span></span>

<span data-ttu-id="41c40-163">对于未按预期工作的任意组合，我们建议在 [EF Core 问题跟踪程序](https://github.com/aspnet/entityframeworkcore/issues/new)中创建新问题。</span><span class="sxs-lookup"><span data-stu-id="41c40-163">For any combination that doesn't work as expected, we encourage creating new issues on the [EF Core issue tracker](https://github.com/aspnet/entityframeworkcore/issues/new).</span></span> <span data-ttu-id="41c40-164">对于特定于 Xamarin 的问题，请使用 [Xamarin.Android](https://github.com/xamarin/xamarin-android/issues/new) 或 [Xamarin.iOS](https://github.com/xamarin/xamarin-macios/issues/new) 问题跟踪程序。</span><span class="sxs-lookup"><span data-stu-id="41c40-164">For Xamarin-specific issues use the issue tracker for [Xamarin.Android](https://github.com/xamarin/xamarin-android/issues/new) or [Xamarin.iOS](https://github.com/xamarin/xamarin-macios/issues/new).</span></span>
