---
title: 从 EF Core 1.0 RC1 升级到 RC2-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 6d75b229-cc79-4d08-88cd-3a1c1b24d88f
uid: core/miscellaneous/rc1-rc2-upgrade
ms.openlocfilehash: 5500addc98a0c0eb314c4d9f9401fa301ce5c6f6
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370521"
---
# <a name="upgrading-from-ef-core-10-rc1-to-10-rc2"></a><span data-ttu-id="abe4b-102">从 EF Core 1.0 RC1 升级到 1.0 RC2</span><span class="sxs-lookup"><span data-stu-id="abe4b-102">Upgrading from EF Core 1.0 RC1 to 1.0 RC2</span></span>

<span data-ttu-id="abe4b-103">本文介绍如何将使用 RC1 包生成的应用程序移动到 RC2。</span><span class="sxs-lookup"><span data-stu-id="abe4b-103">This article provides guidance for moving an application built with the RC1 packages to RC2.</span></span>

## <a name="package-names-and-versions"></a><span data-ttu-id="abe4b-104">包名称和版本</span><span class="sxs-lookup"><span data-stu-id="abe4b-104">Package Names and Versions</span></span>

<span data-ttu-id="abe4b-105">在 RC1 和 RC2 之间，我们将从 "实体框架 7" 改为 "Entity Framework Core"。</span><span class="sxs-lookup"><span data-stu-id="abe4b-105">Between RC1 and RC2, we changed from "Entity Framework 7" to "Entity Framework Core".</span></span> <span data-ttu-id="abe4b-106">可以[通过 Scott Hanselman 详细了解此帖子](https://www.hanselman.com/blog/ASPNET5IsDeadIntroducingASPNETCore10AndNETCore10.aspx)中的更改原因。</span><span class="sxs-lookup"><span data-stu-id="abe4b-106">You can read more about the reasons for the change in [this post by Scott Hanselman](https://www.hanselman.com/blog/ASPNET5IsDeadIntroducingASPNETCore10AndNETCore10.aspx).</span></span> <span data-ttu-id="abe4b-107">由于此更改，我们的包名称将从更改 `EntityFramework.*` 为 `Microsoft.EntityFrameworkCore.*` ，从 `7.0.0-rc1-final` 到 `1.0.0-rc2-final` （或 `1.0.0-preview1-final` 用于工具）的版本。</span><span class="sxs-lookup"><span data-stu-id="abe4b-107">Because of this change, our package names changed from `EntityFramework.*` to `Microsoft.EntityFrameworkCore.*` and our versions from `7.0.0-rc1-final` to `1.0.0-rc2-final` (or `1.0.0-preview1-final` for tooling).</span></span>

<span data-ttu-id="abe4b-108">**需要完全删除 RC1 包，然后安装 RC2 包。**</span><span class="sxs-lookup"><span data-stu-id="abe4b-108">**You will need to completely remove the RC1 packages and then install the RC2 ones.**</span></span> <span data-ttu-id="abe4b-109">下面是一些常用包的映射。</span><span class="sxs-lookup"><span data-stu-id="abe4b-109">Here is the mapping for some common packages.</span></span>

| <span data-ttu-id="abe4b-110">RC1 包</span><span class="sxs-lookup"><span data-stu-id="abe4b-110">RC1 Package</span></span>                                               | <span data-ttu-id="abe4b-111">RC2 等效项</span><span class="sxs-lookup"><span data-stu-id="abe4b-111">RC2 Equivalent</span></span>                                                       |
|:----------------------------------------------------------|:---------------------------------------------------------------------|
| <span data-ttu-id="abe4b-112">EntityFramework. Microsoft sql server 7.0.0-rc1-final</span><span class="sxs-lookup"><span data-stu-id="abe4b-112">EntityFramework.MicrosoftSqlServer        7.0.0-rc1-final</span></span> | <span data-ttu-id="abe4b-113">Microsoft.entityframeworkcore 1.0.0-rc2-最终</span><span class="sxs-lookup"><span data-stu-id="abe4b-113">Microsoft.EntityFrameworkCore.SqlServer         1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="abe4b-114">EntityFramework 7.0.0 版-rc1-最终</span><span class="sxs-lookup"><span data-stu-id="abe4b-114">EntityFramework.SQLite                    7.0.0-rc1-final</span></span> | <span data-ttu-id="abe4b-115">Microsoft.entityframeworkcore 1.0.0-rc2-最终</span><span class="sxs-lookup"><span data-stu-id="abe4b-115">Microsoft.EntityFrameworkCore.Sqlite            1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="abe4b-116">EntityFramework7. Npgsql 3.1.0-rc1</span><span class="sxs-lookup"><span data-stu-id="abe4b-116">EntityFramework7.Npgsql                   3.1.0-rc1-3</span></span>     | <span data-ttu-id="abe4b-117">NpgSql. Microsoft.entityframeworkcore. Postgres<to be advised></span><span class="sxs-lookup"><span data-stu-id="abe4b-117">NpgSql.EntityFrameworkCore.Postgres             <to be advised></span></span>      |
| <span data-ttu-id="abe4b-118">EntityFramework. SqlServerCompact35 7.0.0-rc1-final</span><span class="sxs-lookup"><span data-stu-id="abe4b-118">EntityFramework.SqlServerCompact35        7.0.0-rc1-final</span></span> | <span data-ttu-id="abe4b-119">Microsoft.entityframeworkcore. SqlServerCompact35 1.0.0-rc2-最终</span><span class="sxs-lookup"><span data-stu-id="abe4b-119">EntityFrameworkCore.SqlServerCompact35          1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="abe4b-120">EntityFramework. SqlServerCompact40 7.0.0-rc1-final</span><span class="sxs-lookup"><span data-stu-id="abe4b-120">EntityFramework.SqlServerCompact40        7.0.0-rc1-final</span></span> | <span data-ttu-id="abe4b-121">Microsoft.entityframeworkcore. SqlServerCompact40 1.0.0-rc2-最终</span><span class="sxs-lookup"><span data-stu-id="abe4b-121">EntityFrameworkCore.SqlServerCompact40          1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="abe4b-122">EntityFramework 7.0.0 版-rc1-最终</span><span class="sxs-lookup"><span data-stu-id="abe4b-122">EntityFramework.InMemory                  7.0.0-rc1-final</span></span> | <span data-ttu-id="abe4b-123">Microsoft.entityframeworkcore 1.0.0-rc2-最终</span><span class="sxs-lookup"><span data-stu-id="abe4b-123">Microsoft.EntityFrameworkCore.InMemory          1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="abe4b-124">EntityFramework. IBMDataServer 7.0.0-beta1</span><span class="sxs-lookup"><span data-stu-id="abe4b-124">EntityFramework.IBMDataServer             7.0.0-beta1</span></span>     | <span data-ttu-id="abe4b-125">尚不适用于 RC2</span><span class="sxs-lookup"><span data-stu-id="abe4b-125">Not yet available for RC2</span></span>                                            |
| <span data-ttu-id="abe4b-126">EntityFramework rc1-final</span><span class="sxs-lookup"><span data-stu-id="abe4b-126">EntityFramework.Commands                  7.0.0-rc1-final</span></span> | <span data-ttu-id="abe4b-127">Microsoft.entityframeworkcore 1.0.0-预览版 1-final</span><span class="sxs-lookup"><span data-stu-id="abe4b-127">Microsoft.EntityFrameworkCore.Tools             1.0.0-preview1-final</span></span> |
| <span data-ttu-id="abe4b-128">EntityFramework Microsoft sql server-rc1-final</span><span class="sxs-lookup"><span data-stu-id="abe4b-128">EntityFramework.MicrosoftSqlServer.Design 7.0.0-rc1-final</span></span> | <span data-ttu-id="abe4b-129">Microsoft.entityframeworkcore 1.0.0-rc2-最终</span><span class="sxs-lookup"><span data-stu-id="abe4b-129">Microsoft.EntityFrameworkCore.SqlServer.Design  1.0.0-rc2-final</span></span>      |

## <a name="namespaces"></a><span data-ttu-id="abe4b-130">命名空间</span><span class="sxs-lookup"><span data-stu-id="abe4b-130">Namespaces</span></span>

<span data-ttu-id="abe4b-131">与包名称一起，命名空间从更改 `Microsoft.Data.Entity.*` 为 `Microsoft.EntityFrameworkCore.*` 。</span><span class="sxs-lookup"><span data-stu-id="abe4b-131">Along with package names, namespaces changed from `Microsoft.Data.Entity.*` to `Microsoft.EntityFrameworkCore.*`.</span></span> <span data-ttu-id="abe4b-132">您可以使用的 "查找/替换" 来处理此更改 `using Microsoft.Data.Entity` `using Microsoft.EntityFrameworkCore` 。</span><span class="sxs-lookup"><span data-stu-id="abe4b-132">You can handle this change with a find/replace of `using Microsoft.Data.Entity` with `using Microsoft.EntityFrameworkCore`.</span></span>

## <a name="table-naming-convention-changes"></a><span data-ttu-id="abe4b-133">表命名约定更改</span><span class="sxs-lookup"><span data-stu-id="abe4b-133">Table Naming Convention Changes</span></span>

<span data-ttu-id="abe4b-134">RC2 中的一个重要功能更改是将 `DbSet<TEntity>` 给定实体的属性名称用作其映射到的表名称，而不只是类名。</span><span class="sxs-lookup"><span data-stu-id="abe4b-134">A significant functional change we took in RC2 was to use the name of the `DbSet<TEntity>` property for a given entity as the table name it maps to, rather than just the class name.</span></span> <span data-ttu-id="abe4b-135">有关此更改的详细信息，请参阅[相关公告问题](https://github.com/aspnet/Announcements/issues/167)。</span><span class="sxs-lookup"><span data-stu-id="abe4b-135">You can read more about this change in [the related announcement issue](https://github.com/aspnet/Announcements/issues/167).</span></span>

<span data-ttu-id="abe4b-136">对于现有的 RC1 应用程序，我们建议将以下代码添加到方法的开头， `OnModelCreating` 以保留 RC1 命名策略：</span><span class="sxs-lookup"><span data-stu-id="abe4b-136">For existing RC1 applications, we recommend adding the following code to the start of your `OnModelCreating` method to keep the RC1 naming strategy:</span></span>

``` csharp
foreach (var entity in modelBuilder.Model.GetEntityTypes())
{
    entity.Relational().TableName = entity.DisplayName();
}
```

<span data-ttu-id="abe4b-137">如果要采用新的命名策略，我们建议成功完成其余的升级步骤，并删除代码并创建迁移，以应用表重命名。</span><span class="sxs-lookup"><span data-stu-id="abe4b-137">If you want to adopt the new naming strategy, we would recommend successfully completing the rest of the upgrade steps and then removing the code and creating a migration to apply the table renames.</span></span>

## <a name="adddbcontext--startupcs-changes-aspnet-core-projects-only"></a><span data-ttu-id="abe4b-138">AddDbContext/Startup.cs 更改（仅 ASP.NET Core 项目）</span><span class="sxs-lookup"><span data-stu-id="abe4b-138">AddDbContext / Startup.cs Changes (ASP.NET Core Projects Only)</span></span>

<span data-ttu-id="abe4b-139">在 RC1 中，你必须将实体框架服务添加到应用程序服务提供程序中 `Startup.ConfigureServices(...)` ：</span><span class="sxs-lookup"><span data-stu-id="abe4b-139">In RC1, you had to add Entity Framework services to the application service provider - in `Startup.ConfigureServices(...)`:</span></span>

``` csharp
services.AddEntityFramework()
  .AddSqlServer()
  .AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

<span data-ttu-id="abe4b-140">在 RC2 中，你可以删除对、等的调用 `AddEntityFramework()` `AddSqlServer()` ：</span><span class="sxs-lookup"><span data-stu-id="abe4b-140">In RC2, you can remove the calls to `AddEntityFramework()`, `AddSqlServer()`, etc.:</span></span>

``` csharp
services.AddDbContext<ApplicationDbContext>(options =>
  options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

<span data-ttu-id="abe4b-141">还需要向派生上下文添加一个构造函数，该构造函数采用上下文选项，并将其传递到基构造函数。</span><span class="sxs-lookup"><span data-stu-id="abe4b-141">You also need to add a constructor, to your derived context, that takes context options and passes them to the base constructor.</span></span> <span data-ttu-id="abe4b-142">这是必需的，因为我们消除了在幕后 snuck 它们的一些可怕的神奇之处：</span><span class="sxs-lookup"><span data-stu-id="abe4b-142">This is needed because we removed some of the scary magic that snuck them in behind the scenes:</span></span>

``` csharp
public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
    : base(options)
{
}
```

## <a name="passing-in-an-iserviceprovider"></a><span data-ttu-id="abe4b-143">传入 IServiceProvider</span><span class="sxs-lookup"><span data-stu-id="abe4b-143">Passing in an IServiceProvider</span></span>

<span data-ttu-id="abe4b-144">如果你有将传递给上下文的 RC1 代码 `IServiceProvider` ，则此代码现在已移到 `DbContextOptions` ，而不是单独的构造函数参数。</span><span class="sxs-lookup"><span data-stu-id="abe4b-144">If you have RC1 code that passes an `IServiceProvider` to the context, this has now moved to `DbContextOptions`, rather than being a separate constructor parameter.</span></span> <span data-ttu-id="abe4b-145">使用 `DbContextOptionsBuilder.UseInternalServiceProvider(...)` 设置服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="abe4b-145">Use `DbContextOptionsBuilder.UseInternalServiceProvider(...)` to set the service provider.</span></span>

### <a name="testing"></a><span data-ttu-id="abe4b-146">测试</span><span class="sxs-lookup"><span data-stu-id="abe4b-146">Testing</span></span>

<span data-ttu-id="abe4b-147">执行此操作的最常见方案是在测试时控制 InMemory 数据库的范围。</span><span class="sxs-lookup"><span data-stu-id="abe4b-147">The most common scenario for doing this was to control the scope of an InMemory database when testing.</span></span> <span data-ttu-id="abe4b-148">有关使用 RC2 执行此操作的示例，请参阅更新的[测试](testing/index.md)文章。</span><span class="sxs-lookup"><span data-stu-id="abe4b-148">See the updated [Testing](testing/index.md) article for an example of doing this with RC2.</span></span>

### <a name="resolving-internal-services-from-application-service-provider-aspnet-core-projects-only"></a><span data-ttu-id="abe4b-149">从应用程序服务提供程序解析内部服务（仅 ASP.NET Core 项目）</span><span class="sxs-lookup"><span data-stu-id="abe4b-149">Resolving Internal Services from Application Service Provider (ASP.NET Core Projects Only)</span></span>

<span data-ttu-id="abe4b-150">如果你有一个 ASP.NET Core 应用程序，并且你希望 EF 解析应用程序服务提供程序中的内部服务，则有一个的重载， `AddDbContext` 它允许你配置以下内容：</span><span class="sxs-lookup"><span data-stu-id="abe4b-150">If you have an ASP.NET Core application and you want EF to resolve internal services from the application service provider, there is an overload of `AddDbContext` that allows you to configure this:</span></span>

``` csharp
services.AddEntityFrameworkSqlServer()
  .AddDbContext<ApplicationDbContext>((serviceProvider, options) =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"])
           .UseInternalServiceProvider(serviceProvider));
```

> [!WARNING]  
> <span data-ttu-id="abe4b-151">建议允许 EF 内部管理其自己的服务，除非您有理由将内部 EF 服务合并到您的应用程序服务提供程序中。</span><span class="sxs-lookup"><span data-stu-id="abe4b-151">We recommend allowing EF to internally manage its own services, unless you have a reason to combine the internal EF services into your application service provider.</span></span> <span data-ttu-id="abe4b-152">你可能想要执行此操作的主要原因是使用应用程序服务提供程序来替换 EF 在内部使用的服务</span><span class="sxs-lookup"><span data-stu-id="abe4b-152">The main reason you may want to do this is to use your application service provider to replace services that EF uses internally</span></span>

## <a name="dnx-commands--net-core-cli-aspnet-core-projects-only"></a><span data-ttu-id="abe4b-153">DNX 命令 => .NET Core CLI （仅 ASP.NET Core 项目）</span><span class="sxs-lookup"><span data-stu-id="abe4b-153">DNX Commands => .NET Core CLI (ASP.NET Core Projects Only)</span></span>

<span data-ttu-id="abe4b-154">如果以前使用过 `dnx ef` ASP.NET 5 项目的命令，则这些命令现在已移动到 `dotnet ef` 命令。</span><span class="sxs-lookup"><span data-stu-id="abe4b-154">If you previously used the `dnx ef` commands for ASP.NET 5 projects, these have now moved to `dotnet ef` commands.</span></span> <span data-ttu-id="abe4b-155">相同的命令语法仍适用。</span><span class="sxs-lookup"><span data-stu-id="abe4b-155">The same command syntax still applies.</span></span> <span data-ttu-id="abe4b-156">您可以使用 `dotnet ef --help` 来提供语法信息。</span><span class="sxs-lookup"><span data-stu-id="abe4b-156">You can use `dotnet ef --help` for syntax information.</span></span>

<span data-ttu-id="abe4b-157">命令注册的方式在 RC2 中发生了更改，因为 DNX 被 .NET Core CLI 替换。</span><span class="sxs-lookup"><span data-stu-id="abe4b-157">The way commands are registered has changed in RC2, due to DNX being replaced by the .NET Core CLI.</span></span> <span data-ttu-id="abe4b-158">现在，命令在中的一个 `tools` 部分注册 `project.json` ：</span><span class="sxs-lookup"><span data-stu-id="abe4b-158">Commands are now registered in a `tools` section in `project.json`:</span></span>

``` json
"tools": {
  "Microsoft.EntityFrameworkCore.Tools": {
    "version": "1.0.0-preview1-final",
    "imports": [
      "portable-net45+win8+dnxcore50",
      "portable-net45+win8"
    ]
  }
}
```

> [!TIP]  
> <span data-ttu-id="abe4b-159">如果使用 Visual Studio，现在可以使用程序包管理器控制台来运行 ASP.NET Core 项目的 EF 命令（在 RC1 中不支持此操作）。</span><span class="sxs-lookup"><span data-stu-id="abe4b-159">If you use Visual Studio, you can now use Package Manager Console to run EF commands for ASP.NET Core projects (this was not supported in RC1).</span></span> <span data-ttu-id="abe4b-160">你仍需要注册的部分中的命令 `tools` `project.json` 来执行此操作。</span><span class="sxs-lookup"><span data-stu-id="abe4b-160">You still need to register the commands in the `tools` section of `project.json` to do this.</span></span>

## <a name="package-manager-commands-require-powershell-5"></a><span data-ttu-id="abe4b-161">程序包管理器命令需要 PowerShell 5</span><span class="sxs-lookup"><span data-stu-id="abe4b-161">Package Manager Commands Require PowerShell 5</span></span>

<span data-ttu-id="abe4b-162">如果你在 Visual Studio 中的包管理器控制台中使用实体框架命令，则需要确保已安装 PowerShell 5。</span><span class="sxs-lookup"><span data-stu-id="abe4b-162">If you use the Entity Framework commands in Package Manager Console in Visual Studio, then you will need to ensure you have PowerShell 5 installed.</span></span> <span data-ttu-id="abe4b-163">这是将在下一版本中删除的临时要求（有关详细信息，请参阅[问题 #5327](https://github.com/aspnet/EntityFramework/issues/5327) ）。</span><span class="sxs-lookup"><span data-stu-id="abe4b-163">This is a temporary requirement that will be removed in the next release (see [issue #5327](https://github.com/aspnet/EntityFramework/issues/5327) for more details).</span></span>

## <a name="using-imports-in-projectjson"></a><span data-ttu-id="abe4b-164">使用 project.js上的 "导入"</span><span class="sxs-lookup"><span data-stu-id="abe4b-164">Using "imports" in project.json</span></span>

<span data-ttu-id="abe4b-165">某些 EF Core 的依赖项尚不支持 .NET Standard。</span><span class="sxs-lookup"><span data-stu-id="abe4b-165">Some of EF Core's dependencies do not support .NET Standard yet.</span></span> <span data-ttu-id="abe4b-166">.NET Standard 和 .NET Core 项目中的 EF Core 可能需要将 "导入" 添加到 project.js上以作为临时解决方法。</span><span class="sxs-lookup"><span data-stu-id="abe4b-166">EF Core in .NET Standard and .NET Core projects may require adding "imports" to project.json as a temporary workaround.</span></span>

<span data-ttu-id="abe4b-167">添加 EF 时，NuGet 还原将显示以下错误消息：</span><span class="sxs-lookup"><span data-stu-id="abe4b-167">When adding EF, NuGet restore will display this error message:</span></span>

``` Console
Package Ix-Async 1.2.5 is not compatible with netcoreapp1.0 (.NETCoreApp,Version=v1.0). Package Ix-Async 1.2.5 supports:
  - net40 (.NETFramework,Version=v4.0)
  - net45 (.NETFramework,Version=v4.5)
  - portable-net45+win8+wp8 (.NETPortable,Version=v0.0,Profile=Profile78)
Package Remotion.Linq 2.0.2 is not compatible with netcoreapp1.0 (.NETCoreApp,Version=v1.0). Package Remotion.Linq 2.0.2 supports:
  - net35 (.NETFramework,Version=v3.5)
  - net40 (.NETFramework,Version=v4.0)
  - net45 (.NETFramework,Version=v4.5)
  - portable-net45+win8+wp8+wpa81 (.NETPortable,Version=v0.0,Profile=Profile259)
```

<span data-ttu-id="abe4b-168">解决方法是手动导入可移植配置文件 "net451 + win8"。</span><span class="sxs-lookup"><span data-stu-id="abe4b-168">The workaround is to manually import the portable profile "portable-net451+win8".</span></span> <span data-ttu-id="abe4b-169">这会强制 NuGet 将与此提供的二进制文件相匹配的二进制文件视为与 .NET Standard 兼容的框架，即使它们不是这样。</span><span class="sxs-lookup"><span data-stu-id="abe4b-169">This forces NuGet to treat this binaries that match this provide as a compatible framework with .NET Standard, even though they are not.</span></span> <span data-ttu-id="abe4b-170">尽管 "net451 + win8" 与 .NET Standard 的兼容性不是100%，但其兼容性足以用于从 PCL 转换到 .NET Standard。</span><span class="sxs-lookup"><span data-stu-id="abe4b-170">Although "portable-net451+win8" is not 100% compatible with .NET Standard, it is compatible enough for the transition from PCL to .NET Standard.</span></span> <span data-ttu-id="abe4b-171">当 EF 的依赖项最终升级到 .NET Standard 时，可以删除导入。</span><span class="sxs-lookup"><span data-stu-id="abe4b-171">Imports can be removed when EF's dependencies eventually upgrade to .NET Standard.</span></span>

<span data-ttu-id="abe4b-172">可以在数组语法中将多个框架添加到 "导入"。</span><span class="sxs-lookup"><span data-stu-id="abe4b-172">Multiple frameworks can be added to "imports" in array syntax.</span></span> <span data-ttu-id="abe4b-173">如果将其他库添加到项目，则可能需要其他导入。</span><span class="sxs-lookup"><span data-stu-id="abe4b-173">Other imports may be necessary if you add additional libraries to your project.</span></span>

``` json
{
  "frameworks": {
    "netcoreapp1.0": {
      "imports": ["dnxcore50", "portable-net451+win8"]
    }
  }
}
```

<span data-ttu-id="abe4b-174">请参阅[问题 #5176](https://github.com/aspnet/EntityFramework/issues/5176)。</span><span class="sxs-lookup"><span data-stu-id="abe4b-174">See [Issue #5176](https://github.com/aspnet/EntityFramework/issues/5176).</span></span>
