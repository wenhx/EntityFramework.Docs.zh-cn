---
title: 使用单独的迁移项目-EF Core
description: 使用单独的迁移项目来管理具有 Entity Framework Core 的数据库架构
author: bricelam
ms.date: 11/06/2020
uid: core/managing-schemas/migrations/projects
ms.openlocfilehash: 2d1c093e0bb307584e2bf19cb93deec98aa10692
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429801"
---
# <a name="using-a-separate-migrations-project"></a>使用单独的迁移项目

你可能想要将迁移存储在与包含你的不同的项目中 `DbContext` 。 你还可以使用此策略来维护多个迁移集，例如，一个用于开发，另一个用于发布到发布升级。

> [!TIP]
> 可在 [GitHub 示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Schemas/ThreeProjectMigrations)中查看此文章的示例。

## <a name="steps"></a>步骤

1. 创建一个新的类库。

2. 添加对 DbContext 项目的引用。

3. 将迁移和模型快照文件移动到类库。
   > [!TIP]
   > 如果没有现有迁移，请在包含 DbContext 的项目中生成一个迁移，然后移动它。
   > 这一点很重要，因为如果迁移项目不包含现有迁移，Add-Migration 命令将无法找到 DbContext。

4. 配置迁移程序集：

   [!code-csharp[](../../../../samples/core/Schemas/ThreeProjectMigrations/WebApplication1/Startup.cs#snippet_MigrationsAssembly)]

5. 从 **启动** 项目添加对迁移项目的引用。

   ```xml
   <ItemGroup>
     <ProjectReference Include="..\WebApplication1.Migrations\WebApplication1.Migrations.csproj">
   </ItemGroup>
   ```

   如果这导致循环依赖项，则可以改为更新 **迁移** 项目的基输出路径：

   ```xml
   <PropertyGroup>
     <BaseOutputPath>..\WebApplication1\bin\</BaseOutputPath>
   </PropertyGroup>
   ```

如果一切正常，应能够向项目添加新的迁移。

## <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add NewMigration --project WebApplication1.Migrations
```

## <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration NewMigration -Project WebApplication1.Migrations
```

***
