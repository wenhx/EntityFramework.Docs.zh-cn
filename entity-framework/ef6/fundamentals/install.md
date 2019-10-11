---
title: 获取实体框架-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 122c38a2-f9e8-4ecc-9c72-a83bc9af7814
ms.openlocfilehash: 2bdec6a9be228fbe934d0f46aa1bfafdfb2c971c
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181741"
---
# <a name="get-entity-framework"></a>获取实体框架
实体框架由适用于 Visual Studio 的 EF 工具和 EF 运行时组成。

## <a name="ef-tools-for-visual-studio"></a>适用于 Visual Studio 的 EF 工具

Visual Studio 的 Entity Framework Tools 包括 EF 设计器和 EF 模型向导，并是数据库优先和模型优先工作流所必需的。 所有最新版本的 Visual Studio 中都包含 EF 工具。 如果执行 Visual Studio 的自定义安装，则需要确保选择项 "实体框架6工具"，方法是选择包含它的工作负荷，或将其选择为单个组件。

对于某些早期版本的 Visual Studio，更新的 EF 工具可作为下载。 请参阅[Visual Studio 版本](~/ef6/what-is-new/visual-studio.md)，了解有关如何获取适用于你的 Visual studio 版本的最新版本 EF 工具的指南。

## <a name="ef-runtime"></a>EF 运行时

实体框架的最新版本可作为[EntityFramework NuGet 包](https://nuget.org/packages/EntityFramework/)提供。 如果你不熟悉 NuGet 包管理器，我们建议你阅读[Nuget 概述](https://docs.microsoft.com/nuget/consume-packages/overview-and-workflow)。

### <a name="installing-the-ef-nuget-package"></a>安装 EF NuGet 包

可以通过右键单击项目的 "**引用**" 文件夹并选择 "**管理 NuGet 包 ...** " 来安装 EntityFramework 包。

![管理 NuGet 包](~/ef6/media/managenugetpackages.png)

### <a name="installing-from-package-manager-console"></a>从包管理器控制台安装

或者，你可以通过在[包管理器控制台](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)中运行以下命令来安装 EntityFramework。

``` powershell
Install-Package EntityFramework
```

## <a name="installing-a-specific-version-of-ef"></a>安装特定版本的 EF

从 EF 4.1 开始，已发布了 EF 运行时的新版本作为[EntityFramework NuGet 包](https://www.nuget.org/packages/EntityFramework/)。 可以通过在 Visual Studio 的[包管理器控制台](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)中运行以下命令，将这些版本中的任何一种添加到基于 .NET Framework 的项目中：

``` powershell
Install-Package EntityFramework -Version <number>
```

请注意，@no__t 表示要安装的 EF 的特定版本。 例如，6.2.0 是 EF 6.2 的编号版本。   

4\.1 之前的 EF 运行时是 .NET Framework 的一部分，不能单独安装。

### <a name="installing-the-latest-preview"></a>安装最新预览版

上述方法将为你介绍实体框架的最新完全受支持的版本。 实体框架提供了预发布版本的预发布版本，我们希望你尝试并向我们提供反馈。

若要安装最新的 EntityFramework 预览版，可以在 "管理 NuGet 包" 窗口中选择 "**包括预发行**版"。 如果没有可用的预发布版本，则会自动获取实体框架的最新完全受支持版本。

![包括预发行版](~/ef6/media/includeprerelease.png)

或者，你可以在[包管理器控制台](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)中运行以下命令。

``` powershell
Install-Package EntityFramework -Pre
```
