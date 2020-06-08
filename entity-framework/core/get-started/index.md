---
title: 入门 - EF Core
author: rick-anderson
ms.date: 09/17/2019
ms.assetid: 3c88427c-20c6-42ec-a736-22d3eccd5071
uid: core/get-started/index
ms.openlocfilehash: b45a7e5010dd3ef37ea952aaf483847ae95ea4ef
ms.sourcegitcommit: 1f0f93c66b2b50e03fcbed90260e94faa0279c46
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2020
ms.locfileid: "84418933"
---
# <a name="getting-started-with-ef-core"></a>EF Core 入门

在本教程中，将创建一个 .NET Core 控制台应用，该应用使用 Entity Framework Core 对 SQLite 数据库执行数据访问。

你可在 Windows 上使用 Visual Studio，或在 Windows、macOS 或 Linux 上使用 .NET Core CLI 来学习本教程。

[在 GitHub 上查看此文章的示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/GetStarted)。

## <a name="prerequisites"></a>先决条件

安装以下软件：

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

* [.NET Core SDK](https://www.microsoft.com/net/download/core)。

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 对于此工作负载，建议采用 [Visual Studio 2019 版本 16.3 或更高版本](https://www.visualstudio.com/downloads/)：
  * “.NET Core 跨平台开发”（位于“其他工具集”下）

---

## <a name="create-a-new-project"></a>创建新项目

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet new console -o EFGetStarted
cd EFGetStarted
```

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 打开 Visual Studio
* 单击“创建新项目”
* 选择带有 C# 标记的“控制台应用 (.NET Core)” ，然后单击“下一步”
* 输入“EFGetStarted”作为名称，然后单击“创建”

---

## <a name="install-entity-framework-core"></a>安装 Entity Framework Core

要安装 EF Core，请为要作为目标对象的 EF Core 数据库提供程序安装程序包。 本教程使用 SQLite 的原因是，它可在 .NET Core 支持的所有平台上运行。 有关可用提供程序的列表，请参阅[数据库提供程序](../providers/index.md)。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* “工具”>“NuGet 包管理器”>“包管理器控制台”
* 运行以下命令：

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Sqlite
  ```

提示:还可以通过右键单击项目并选择“管理 NuGet 程序包”来安装包

---

## <a name="create-the-model"></a>创建模型

定义构成模型的上下文类和实体类。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

* 在项目文件夹中，使用以下代码创建 Model.cs

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 右键单击项目，然后选择“添加”>“类”
* 输入“Model.cs”作为名称，然后单击“添加”
* 将此文件的内容替换为以下代码

---

[!code-csharp[Main](../../../samples/core/GetStarted/Model.cs)]

EF Core 还可以从现有数据库对模型进行[反向工程](../managing-schemas/scaffolding.md)。

提示:为清楚起见，有意简化了此应用程序。 [连接字符串](../miscellaneous/connection-strings.md)不应存储在生产应用程序的代码中。 可能还需要将每个 C# 类拆分为其自己的文件。

## <a name="create-the-database"></a>创建数据库

以下步骤使用[迁移](xref:core/managing-schemas/migrations/index)创建数据库。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

* 运行以下命令：

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  这会安装 [dotnet ef](../miscellaneous/cli/dotnet.md) 和设计包，这是对项目运行命令所必需的。 `migrations` 命令为迁移搭建基架，以便为模型创建一组初始表。 `database update` 命令创建数据库并向其应用新的迁移。

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 在“包管理器控制台”中运行以下命令

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Tools
  Add-Migration InitialCreate
  Update-Database
  ```

  这会安装 [ EF Core 的 PMC 工具](../miscellaneous/cli/powershell.md)。 `Add-Migration` 命令为迁移搭建基架，以便为模型创建一组初始表。 `Update-Database` 命令创建数据库并向其应用新的迁移。

---

## <a name="create-read-update--delete"></a>创建、读取、更新和删除

* 打开 Program.cs 并将内容替换为以下代码：

  [!code-csharp[Main](../../../samples/core/GetStarted/Program.cs)]

## <a name="run-the-app"></a>运行应用

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet run
```

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

运行 .NET Core 控制台应用时，Visual Studio 使用不一致的工作目录。 （请参阅 [dotnet/project-system#3619](https://github.com/dotnet/project-system/issues/3619)）这会导致引发异常：*无此类表格：博客*。 更新工作目录：

* 右键单击项目，并选择“编辑项目文件”
* 在 TargetFramework 属性下方，添加以下内容：

  ``` XML
  <StartWorkingDirectory>$(MSBuildProjectDirectory)</StartWorkingDirectory>
  ```

* 保存该文件

现在可以运行应用：

* “调试”>“开始执行(不调试)”

---

## <a name="next-steps"></a>后续步骤

* 按照 [ASP.NET Core 教程](/aspnet/core/data/ef-rp/intro)在 Web 应用中使用 EF Core
* 了解有关 [LINQ 查询表达式](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)的详细信息
* [配置模型](xref:core/modeling/index)指定[必需项](xref:core/modeling/entity-properties#required-and-optional-properties)和[最大长度](xref:core/modeling/entity-properties#maximum-length)等内容
* 在更改模型后使用[迁移](xref:core/managing-schemas/migrations/index)来更新数据库架构
