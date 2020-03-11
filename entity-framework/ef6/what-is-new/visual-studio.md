---
title: Visual Studio 版本-EF6
author: divega
ms.date: 07/05/2018
ms.assetid: 028FF890-4EDB-4F03-AE53-72F9C33EC92F
ms.openlocfilehash: 16bcdc6d0e7c5632d4f4c06ba285a7a666f24204
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414355"
---
# <a name="visual-studio-releases"></a>Visual Studio 版本

建议始终使用最新版本的 Visual Studio，因为它包含适用于 .NET、NuGet 和实体框架的最新工具。
事实上，实体框架文档中的各种示例和演练都假设你使用的是最新版本的 Visual Studio。

不过，只要考虑一些差异，就有可能使用不同版本的 Visual Studio 的较旧版本实体框架：

## <a name="visual-studio-2017-157-and-newer"></a>Visual Studio 2017 15.7 及更高版本

- 此版本的 Visual Studio 包括实体框架工具和 EF 6.2 运行时的最新版本，不需要执行其他设置步骤。
有关这些版本的详细信息，请参阅[新增功能](~/ef6/what-is-new/index.md)。
- 使用 EF 工具将实体框架添加到新项目将自动添加 EF 6.2 NuGet 包。
可以手动安装或升级到联机提供的任何 EF NuGet 包。
- 默认情况下，此版本的 Visual Studio 中可用的 SQL Server 实例为 LocalDB 实例，名为 MSSQLLocalDB。
应使用的连接字符串的服务器部分为 "（localdb）\\MSSQLLocalDB"。
在代码中C#指定连接字符串时，请记住使用前缀为 `@` 或双反斜杠 "\\\\" 的逐字字符串。  


## <a name="visual-studio-2015-to-visual-studio-2017-156"></a>Visual Studio 2015 到 Visual Studio 2017 15。6

- 这些版本的 Visual Studio 包括实体框架工具和运行时 ef6.1.3。
有关这些版本的详细信息，请参阅[以前的版本](~/ef6/what-is-new/past-releases.md#ef-613)。
- 使用 EF 工具向新项目添加实体框架会自动添加 EF ef6.1.3 NuGet 包。
可以手动安装或升级到联机提供的任何 EF NuGet 包。
- 默认情况下，此版本的 Visual Studio 中可用的 SQL Server 实例为 LocalDB 实例，名为 MSSQLLocalDB。
应使用的连接字符串的服务器部分为 "（localdb）\\MSSQLLocalDB"。
在代码中C#指定连接字符串时，请记住使用前缀为 `@` 或双反斜杠 "\\\\" 的逐字字符串。  


## <a name="visual-studio-2013"></a>Visual Studio 2013
- 此版本的 Visual Studio 包括实体框架工具和运行时的旧版本。
建议使用 Microsoft 下载中心中提供[的安装程序](https://www.microsoft.com/download/details.aspx?id=40762)Entity Framework Tools ef6.1.3 升级到。
有关这些版本的详细信息，请参阅[以前的版本](~/ef6/what-is-new/past-releases.md#ef-613)。
- 使用升级后的 EF 工具向新项目添加实体框架会自动添加 EF ef6.1.3 NuGet 包。
可以手动安装或升级到联机提供的任何 EF NuGet 包。
- 默认情况下，此版本的 Visual Studio 中可用的 SQL Server 实例为 LocalDB 实例，名为 MSSQLLocalDB。
应使用的连接字符串的服务器部分为 "（localdb）\\MSSQLLocalDB"。
在代码中C#指定连接字符串时，请记住使用前缀为 `@` 或双反斜杠 "\\\\" 的逐字字符串。  

## <a name="visual-studio-2012"></a>Visual Studio 2012

- 此版本的 Visual Studio 包括实体框架工具和运行时的旧版本。
建议使用 Microsoft 下载中心中提供[的安装程序](https://www.microsoft.com/download/details.aspx?id=40762)Entity Framework Tools ef6.1.3 升级到。
有关这些版本的详细信息，请参阅[以前的版本](~/ef6/what-is-new/past-releases.md#ef-613)。
- 使用升级后的 EF 工具向新项目添加实体框架会自动添加 EF ef6.1.3 NuGet 包。
可以手动安装或升级到联机提供的任何 EF NuGet 包。
- 默认情况下，此版本的 Visual Studio 中可用的 SQL Server 实例是名为 "v 11.0" 的 LocalDB 实例。
应使用的连接字符串的服务器部分为 "（localdb）\\v 11.0"。
在代码中C#指定连接字符串时，请记住使用前缀为 `@` 或双反斜杠 "\\\\" 的逐字字符串。  

## <a name="visual-studio-2010"></a>Visual Studio 2010

- 此版本的 Visual Studio 中提供的 Entity Framework Tools 版本与实体框架6运行时不兼容，因此无法升级。
- 默认情况下，实体框架工具会将实体框架4.0 添加到你的项目。
若要使用任何更高版本的 EF 创建应用程序，首先需要安装[NuGet 包管理器扩展](https://marketplace.visualstudio.com/items?itemName=NuGetTeam.NuGetPackageManager)。
- 默认情况下，EF 工具版本中的所有代码生成都基于 EntityObject 和实体框架4。
建议通过安装[C#](https://marketplace.visualstudio.com/items?itemName=EntityFrameworkTeam.EF5xDbContextGeneratorforC)或[Visual Basic](https://marketplace.visualstudio.com/items?itemName=EntityFrameworkTeam.EF5xDbContextGeneratorforVBNET)的 DbContext 代码生成模板，将代码生成切换为基于 DbContext 和实体框架5。
- 安装 NuGet 包管理器扩展后，可以手动安装或升级到任何联机可用的 EF NuGet 包，并将 EF6 与 Code First 一起使用，这不需要设计器。
- 默认情况下，此版本的 Visual Studio 中提供的 SQL Server 实例 SQL Server Express 称为 SQLEXPRESS。
应使用的连接字符串的服务器部分是 ""。\\SQLEXPRESS "。
在代码中C#指定连接字符串时，请记住使用前缀为 `@` 或双反斜杠 "\\\\" 的逐字字符串。
