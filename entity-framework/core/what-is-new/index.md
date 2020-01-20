---
title: EF Core 版本和计划
author: ajcvickers
ms.date: 01/14/2020
ms.assetid: C21F89EE-FB08-4ED9-A2A0-76CB7656E6E4
uid: core/what-is-new/index
ms.openlocfilehash: 8d74c24021fd62c5c5d944eaf3973b344fdb1e9c
ms.sourcegitcommit: f2a38c086291699422d8b28a72d9611d1b24ad0d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76124400"
---
# <a name="ef-core-releases-and-planning"></a>EF Core 版本和计划

## <a name="stable-releases"></a>稳定版本

| Release | 目标 Framework | 支持截止时间 | 链接
|:--------|------------------|-----------------|------
| [EF Core 3.1](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/3.1.1) | .NET Standard 2.0 | 2022 年 12 月 3 日 (LTS) | [公告](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-3-1-and-entity-framework-6-4/)
| [EF Core 3.0](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/3.0.1) | .NET Standard 2.1 | 2020 年 3 月 3 日 | [公告](https://devblogs.microsoft.com/dotnet/announcing-ef-core-3-0-and-ef-6-3-general-availability/) / [重大更改](ef-core-3.0/breaking-changes.md)
| ~~[EF Core 2.2](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/2.2.6)~~ | .NET Standard 2.0 | 过期时间：2019 年 12 月 23 日 | [公告](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-2-2/)
| [EF Core 2.1](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/2.1.14) | .NET Standard 2.0 | 2021 年 8 月 21 日 (LTS) | [公告](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-2-1/)
| ~~[EF Core 2.0](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/2.0.3)~~ | .NET Standard 2.0 | 过期时间：2018 年 10 月 1 日 | [公告](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-2-0/)
| ~~[EF Core 1.1](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/1.1.6)~~ | .NET Standard 1.3 | 过期时间：2019 年 6 月 27 日 | [公告](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-1-1/)
| ~~[EF Core 1.0](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/1.0.6)~~ | .NET Standard 1.3 | 过期时间：2019 年 6 月 27 日 | [公告](https://devblogs.microsoft.com/dotnet/entity-framework-core-1-0-0-available/)

有关每个 EF Core 版本支持的特定平台的信息，请参阅[支持的平台](../platforms/index.md)。

有关支持到期时间和长期支持 (LTS) 版本的信息，请参阅 [.NET 支持策略](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。

## <a name="guidance-on-updating-to-new-releases"></a>更新到新版本的指南

* 修补了受支持版本的安全 bug 和其他严重 bug。 始终使用给定版本的最新修补程序。 例如，对于 EF Core 2.1，使用 2.1.14。
* 主版本更新（例如，从 EF Core 2 更新到 EF Core 3）通常包含重大更改。 在进行主版本更新时，建议进行全面测试。 使用上面的“重大更改”链接获取有关处理重大更改的指导。
* 次要版本更新通常不包含重大更改。 但仍建议进行全面测试，因为新功能可能会引入回归。

## <a name="ef-core-50"></a>EF Core 5.0

EF Core 版本与 [.NET Core 发货计划](https://github.com/dotnet/core/blob/master/roadmap.md)保持一致。 下一个计划的稳定版本是 **EF Core 5.0**，计划于 2020 年 11 月发布。

已根据记录的[发布计划流程](release-planning.md)创建了 [EF Core 5.0 的高级计划](ef-core-5.0/plan.md)。

你对计划的反馈非常重要。 指示问题重要性的最佳方式是在 GitHub 上为该问题投票（竖起大拇指）。 然后，此数据将进入下一个版本的计划过程。

### <a name="get-it-now"></a>立即获取！

EF Core 5.0 包现在以[每日生成](https://github.com/aspnet/AspNetCore/blob/master/docs/DailyBuilds.md)的形式提供  。 

使用每日生成是一种查找问题并尽快提供反馈的绝佳方式。 我们获取此类反馈的速度越快，就越有可能在发布下一个正式版本前实施该反馈。 我们针对每个生成在每个平台上运行超过 55,000 个测试，致力于使每日生成保持良好状态。

预览包将在今年晚些时候发送到 NuGet。
