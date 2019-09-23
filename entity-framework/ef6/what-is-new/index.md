---
title: 新增功能 - EF6
author: divega
ms.date: 09/12/2019
ms.assetid: 41d1f86b-ce66-4bf2-8963-48514406fb4c
ms.openlocfilehash: 568790d9c9bb7dd2213907bef8fa090710cd3ba0
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149127"
---
# <a name="whats-new-in-ef6"></a>EF6 中的新增功能

强烈建议使用最新发布的实体框架版本，以确保获得最新功能和最高稳定性。
但我们也意识到用户可能需要使用以前的版本，或想要尝试最新预发行版中的新改进。
若要安装特定版本的 EF，请参阅[获取实体框架](~/ef6/fundamentals/install.md)。

## <a name="ef-630"></a>EF 6.3.0

EF 6.3.0 运行时已于 2019 年 9 月发布到 NuGet。 此版本的主要目标是帮助将使用 EF 6 的现有应用程序迁移到 .NET Core 3.0。 社区还提供了多个 bug 修复和增强功能。 有关详细信息，请参阅每个 6.3.0 [里程碑](https://github.com/aspnet/EntityFramework6/milestones?state=closed)中关闭的问题。 下面是一些更值得注意的事项：

- 支持 .NET Core 3.0
  - 除了 .NET Framework 4.x，EntityFramework 包现在还面向 .NET Standard 2.1
  - 已重新编写迁移命令以在进程外执行，并使用 SDK 样式的项目
- 支持 SQL Server HierarchyId
- 提高了与 Roslyn 和 NuGet PackageReference 的兼容性
- 添加了 ef6.exe，用于启用、添加、编写脚本和应用程序集中的迁移。 这会替换 migrate.exe

## <a name="past-releases"></a>过去的版本

[过去的版本](past-releases.md)页包含过去所有版本的 EF 的存档以及各版本中引入的主要功能。
