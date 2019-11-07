---
title: 使用单独的迁移项目-EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/migrations/projects
ms.openlocfilehash: 0c08855db77470d28e23f9ef1d147497dfcdff83
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655558"
---
# <a name="using-a-separate-migrations-project"></a><span data-ttu-id="d7c24-102">使用单独的迁移项目</span><span class="sxs-lookup"><span data-stu-id="d7c24-102">Using a Separate Migrations Project</span></span>

<span data-ttu-id="d7c24-103">你可能想要将迁移存储在与包含你的 `DbContext`的程序集不同的程序集中。</span><span class="sxs-lookup"><span data-stu-id="d7c24-103">You may want to store your migrations in a different assembly than the one containing your `DbContext`.</span></span> <span data-ttu-id="d7c24-104">你还可以使用此策略来维护多个迁移集，例如，一个用于开发，另一个用于发布到发布升级。</span><span class="sxs-lookup"><span data-stu-id="d7c24-104">You can also use this strategy to maintain multiple sets of migrations, for example, one for development and another for release-to-release upgrades.</span></span>

<span data-ttu-id="d7c24-105">若要执行此操作...</span><span class="sxs-lookup"><span data-stu-id="d7c24-105">To do this...</span></span>

1. <span data-ttu-id="d7c24-106">创建一个新的类库。</span><span class="sxs-lookup"><span data-stu-id="d7c24-106">Create a new class library.</span></span>

2. <span data-ttu-id="d7c24-107">添加对 DbContext 程序集的引用。</span><span class="sxs-lookup"><span data-stu-id="d7c24-107">Add a reference to your DbContext assembly.</span></span>

3. <span data-ttu-id="d7c24-108">将迁移和模型快照文件移动到类库。</span><span class="sxs-lookup"><span data-stu-id="d7c24-108">Move the migrations and model snapshot files to the class library.</span></span>
   > [!TIP]
   > <span data-ttu-id="d7c24-109">如果没有现有迁移，请在包含 DbContext 的项目中生成一个迁移，然后移动它。</span><span class="sxs-lookup"><span data-stu-id="d7c24-109">If you have no existing migrations, generate one in the project containing the DbContext then move it.</span></span>
   > <span data-ttu-id="d7c24-110">这一点很重要，因为如果迁移程序集不包含现有迁移，则添加迁移命令将无法找到 DbContext。</span><span class="sxs-lookup"><span data-stu-id="d7c24-110">This is important because if the migrations assembly does not contain an existing migration, the Add-Migration command will be unable to find the DbContext.</span></span>

4. <span data-ttu-id="d7c24-111">配置迁移程序集：</span><span class="sxs-lookup"><span data-stu-id="d7c24-111">Configure the migrations assembly:</span></span>

   ``` csharp
   options.UseSqlServer(
       connectionString,
       x => x.MigrationsAssembly("MyApp.Migrations"));
   ```

5. <span data-ttu-id="d7c24-112">从启动程序集添加对迁移程序集的引用。</span><span class="sxs-lookup"><span data-stu-id="d7c24-112">Add a reference to your migrations assembly from the startup assembly.</span></span>
   * <span data-ttu-id="d7c24-113">如果这导致循环依赖项，请更新类库的输出路径：</span><span class="sxs-lookup"><span data-stu-id="d7c24-113">If this causes a circular dependency, update the output path of the class library:</span></span>

     ``` xml
     <PropertyGroup>
       <OutputPath>..\MyStartupProject\bin\$(Configuration)\</OutputPath>
     </PropertyGroup>
     ```

<span data-ttu-id="d7c24-114">如果一切正常，应能够向项目添加新的迁移。</span><span class="sxs-lookup"><span data-stu-id="d7c24-114">If you did everything correctly, you should be able to add new migrations to the project.</span></span>

## <a name="net-core-clitabdotnet-core-cli"></a>[<span data-ttu-id="d7c24-115">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="d7c24-115">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

``` Console
dotnet ef migrations add NewMigration --project MyApp.Migrations
```

## <a name="visual-studiotabvs"></a>[<span data-ttu-id="d7c24-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d7c24-116">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration NewMigration -Project MyApp.Migrations
```

***
