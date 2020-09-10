---
title: 使用单独的迁移项目-EF Core
description: 使用单独的迁移项目来管理具有 Entity Framework Core 的数据库架构
author: bricelam
ms.author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/migrations/projects
ms.openlocfilehash: a3f0ed96c6a8e3e8629d9a4bb1610fcbfe6ca043
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617919"
---
# <a name="using-a-separate-migrations-project"></a>使用单独的迁移项目

你可能想要将迁移存储在与包含你的不同的程序集中 `DbContext` 。 你还可以使用此策略来维护多个迁移集，例如，一个用于开发，另一个用于发布到发布升级。

若要执行此操作...

1. 创建一个新的类库。

2. 添加对 DbContext 程序集的引用。

3. 将迁移和模型快照文件移动到类库。
   > [!TIP]
   > 如果没有现有迁移，请在包含 DbContext 的项目中生成一个迁移，然后移动它。
   > 这一点很重要，因为如果迁移程序集不包含现有迁移，则添加迁移命令将无法找到 DbContext。

4. 配置迁移程序集：

   ``` csharp
   options.UseSqlServer(
       connectionString,
       x => x.MigrationsAssembly("MyApp.Migrations"));
   ```

5. 从启动程序集添加对迁移程序集的引用。
   * 如果这导致循环依赖项，请更新类库的输出路径：

     ``` xml
     <PropertyGroup>
       <OutputPath>..\MyStartupProject\bin\$(Configuration)\</OutputPath>
     </PropertyGroup>
     ```

如果一切正常，应能够向项目添加新的迁移。

## <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add NewMigration --project MyApp.Migrations
```

## <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Add-Migration NewMigration -Project MyApp.Migrations
```

***
