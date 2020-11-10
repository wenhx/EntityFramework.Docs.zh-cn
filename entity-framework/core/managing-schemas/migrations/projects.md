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
# <a name="using-a-separate-migrations-project"></a><span data-ttu-id="3dbc2-103">使用单独的迁移项目</span><span class="sxs-lookup"><span data-stu-id="3dbc2-103">Using a Separate Migrations Project</span></span>

<span data-ttu-id="3dbc2-104">你可能想要将迁移存储在与包含你的不同的项目中 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="3dbc2-104">You may want to store your migrations in a different project than the one containing your `DbContext`.</span></span> <span data-ttu-id="3dbc2-105">你还可以使用此策略来维护多个迁移集，例如，一个用于开发，另一个用于发布到发布升级。</span><span class="sxs-lookup"><span data-stu-id="3dbc2-105">You can also use this strategy to maintain multiple sets of migrations, for example, one for development and another for release-to-release upgrades.</span></span>

> [!TIP]
> <span data-ttu-id="3dbc2-106">可在 [GitHub 示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Schemas/ThreeProjectMigrations)中查看此文章的示例。</span><span class="sxs-lookup"><span data-stu-id="3dbc2-106">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Schemas/ThreeProjectMigrations).</span></span>

## <a name="steps"></a><span data-ttu-id="3dbc2-107">步骤</span><span class="sxs-lookup"><span data-stu-id="3dbc2-107">Steps</span></span>

1. <span data-ttu-id="3dbc2-108">创建一个新的类库。</span><span class="sxs-lookup"><span data-stu-id="3dbc2-108">Create a new class library.</span></span>

2. <span data-ttu-id="3dbc2-109">添加对 DbContext 项目的引用。</span><span class="sxs-lookup"><span data-stu-id="3dbc2-109">Add a reference to your DbContext project.</span></span>

3. <span data-ttu-id="3dbc2-110">将迁移和模型快照文件移动到类库。</span><span class="sxs-lookup"><span data-stu-id="3dbc2-110">Move the migrations and model snapshot files to the class library.</span></span>
   > [!TIP]
   > <span data-ttu-id="3dbc2-111">如果没有现有迁移，请在包含 DbContext 的项目中生成一个迁移，然后移动它。</span><span class="sxs-lookup"><span data-stu-id="3dbc2-111">If you have no existing migrations, generate one in the project containing the DbContext then move it.</span></span>
   > <span data-ttu-id="3dbc2-112">这一点很重要，因为如果迁移项目不包含现有迁移，Add-Migration 命令将无法找到 DbContext。</span><span class="sxs-lookup"><span data-stu-id="3dbc2-112">This is important because if the migrations project does not contain an existing migration, the Add-Migration command will be unable to find the DbContext.</span></span>

4. <span data-ttu-id="3dbc2-113">配置迁移程序集：</span><span class="sxs-lookup"><span data-stu-id="3dbc2-113">Configure the migrations assembly:</span></span>

   [!code-csharp[](../../../../samples/core/Schemas/ThreeProjectMigrations/WebApplication1/Startup.cs#snippet_MigrationsAssembly)]

5. <span data-ttu-id="3dbc2-114">从 **启动** 项目添加对迁移项目的引用。</span><span class="sxs-lookup"><span data-stu-id="3dbc2-114">Add a reference to your migrations project from the **startup** project.</span></span>

   ```xml
   <ItemGroup>
     <ProjectReference Include="..\WebApplication1.Migrations\WebApplication1.Migrations.csproj">
   </ItemGroup>
   ```

   <span data-ttu-id="3dbc2-115">如果这导致循环依赖项，则可以改为更新 **迁移** 项目的基输出路径：</span><span class="sxs-lookup"><span data-stu-id="3dbc2-115">If this causes a circular dependency, you can update the base output path of the **migrations** project instead:</span></span>

   ```xml
   <PropertyGroup>
     <BaseOutputPath>..\WebApplication1\bin\</BaseOutputPath>
   </PropertyGroup>
   ```

<span data-ttu-id="3dbc2-116">如果一切正常，应能够向项目添加新的迁移。</span><span class="sxs-lookup"><span data-stu-id="3dbc2-116">If you did everything correctly, you should be able to add new migrations to the project.</span></span>

## <a name="net-core-cli"></a>[<span data-ttu-id="3dbc2-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3dbc2-117">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add NewMigration --project WebApplication1.Migrations
```

## <a name="visual-studio"></a>[<span data-ttu-id="3dbc2-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3dbc2-118">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration NewMigration -Project WebApplication1.Migrations
```

***
