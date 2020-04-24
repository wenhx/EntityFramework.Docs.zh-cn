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
# <a name="net-implementations-supported-by-ef-core"></a>EF Core 支持的 .NET 实现

我们希望 EF Core 可供开发人员在所有新式 .NET 实现上使用，并且我们仍在努力实现这一目标。 尽管自动测试证明 EF Core 支持 .NET Core，同时许多应用程序已成功使用 EF Core，但 Mono、Xamarin 和 UWP 仍存在一些问题。

## <a name="overview"></a>概述

下表提供了每个 .NET 实现的指南：

| EF Core                       | 2.1 和 3.1 |
|:------------------------------|:------------|
| .NET Standard                 | 2.0         |
| .NET Core                     | 2.0         |
| .NET Framework<sup>(1)</sup>  | 4.7.2       |
| Mono                          | 5.4         |
| Xamarin.iOS<sup>(2)</sup>     | 10.14       |
| Xamarin.Android<sup>(2)</sup> | 8.0         |
| UWP<sup>(3)</sup>             | 10.0.16299  |
| Unity<sup>(4)</sup>           | 2018 年 1 月      |

<sup>(1)</sup> 请参阅下面的 [.NET Framework](#net-framework) 部分。

<sup>(2)</sup> Xamarin 存在一些问题和已知限制，这些问题和限制可能会阻止部分使用 EF Core 开发的应用程序正常运行。 查看[未解决问题](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-xamarin)列表，了解解决方法。

<sup>(3)</sup> 建议使用 EF Core 2.0.1 和更高版本。 安装 [.NET Core UWP 6.x 包](https://www.nuget.org/packages/Microsoft.NETCore.UniversalWindowsPlatform/)。 请参阅本文的[通用 Windows 平台](#universal-windows-platform)部分。

<sup>(4)</sup> Unity 存在问题和已知限制。 查看[未解决问题](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-unity)列表。

## <a name="net-framework"></a>.NET Framework

面向 .NET Framework 的应用程序可能需要更改为使用 .NET Standard 库：

编辑项目文件，并确保以下条目出现在初始属性组中：

``` xml
<AutoGenerateBindingRedirects>true</AutoGenerateBindingRedirects>
```

对于测试项目，还要确保存在以下条目：

``` xml
<GenerateBindingRedirectsOutputType>true</GenerateBindingRedirectsOutputType>
```

如果想要使用较旧版本的 Visual Studio，请确保[将 NuGet 客户端升级到版本 3.6.0](https://www.nuget.org/downloads)，以便使用 .NET Standard 2.0 库。

如果可能，我们还建议从 NuGet packages.config 迁移到 PackageReference。 请将以下属性添加到项目文件：

``` xml
<RestoreProjectStyle>PackageReference</RestoreProjectStyle>
```

## <a name="universal-windows-platform"></a>通用 Windows 平台

早期的 EF Core 和 .NET UWP 版本存在许多兼容性问题，尤其是用于使用 .NET Native 工具链编译的应用程序时。 新的 .NET UWP 版本增加了对 .NET Standard 2.0 的支持，且包含了 .NET Native 2.0，修复了之前报告的大多数兼容性问题。 我们使用 UWP 对 EF Core 2.0.1 进行了更彻底的测试，但测试不是自动执行的。

在 UWP 上使用 EF Core 时：

* 若要优化查询性能，请避免在 LINQ 查询中使用匿名类型。 将 UWP 应用程序部署到应用商店要求使用 .NET Native 编译应用程序。 使用匿名类型的查询在 .NET Native 上性能较差。

* 若要优化 `SaveChanges()` 性能，请使用 [ChangeTrackingStrategy.ChangingAndChangedNotifications](/dotnet/api/microsoft.entityframeworkcore.changetrackingstrategy)，并在你的实体类型中实施 [INotifyPropertyChanged](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanged.aspx)、[INotifyPropertyChanging](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanging.aspx) 和 [INotifyCollectionChanged](https://msdn.microsoft.com/library/system.collections.specialized.inotifycollectionchanged.aspx)。

## <a name="report-issues"></a>报告问题

对于未按预期工作的任意组合，我们建议在 [EF Core 问题跟踪程序](https://github.com/aspnet/entityframeworkcore/issues/new)中创建新问题。 对于特定于 Xamarin 的问题，请使用 [Xamarin.Android](https://github.com/xamarin/xamarin-android/issues/new) 或 [Xamarin.iOS](https://github.com/xamarin/xamarin-macios/issues/new) 问题跟踪程序。
