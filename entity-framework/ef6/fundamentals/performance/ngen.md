---
title: 通过 NGen 提高启动性能 EF6
description: 在实体框架6中提高 NGen 的启动性能
author: divega
ms.date: 10/23/2016
ms.assetid: dc6110a0-80a0-4370-8190-cea942841cee
uid: ef6/fundamentals/performance/ngen
ms.openlocfilehash: 53b9c2147c95fe096d459ad195a0549b32b67155
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616092"
---
# <a name="improving-startup-performance-with-ngen"></a><span data-ttu-id="5bc93-103">通过 NGen 改善启动性能</span><span class="sxs-lookup"><span data-stu-id="5bc93-103">Improving startup performance with NGen</span></span>
> [!NOTE]
> <span data-ttu-id="5bc93-104">**仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。</span><span class="sxs-lookup"><span data-stu-id="5bc93-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="5bc93-105">如果使用的是早期版本，则部分或全部信息不适用。</span><span class="sxs-lookup"><span data-stu-id="5bc93-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="5bc93-106">.NET Framework 支持为托管应用程序和库生成本机映像，以帮助应用程序的启动速度更快，在某些情况下还会使用更少的内存。</span><span class="sxs-lookup"><span data-stu-id="5bc93-106">The .NET Framework supports the generation of native images for managed applications and libraries as a way to help applications start faster and also in some cases use less memory.</span></span> <span data-ttu-id="5bc93-107">本机映像是通过在执行应用程序之前将托管代码程序集转换为包含本机计算机指令的文件创建的，从而使 .NET JIT (实时) 编译器无需在应用程序运行时生成本机指令。</span><span class="sxs-lookup"><span data-stu-id="5bc93-107">Native images are created by translating managed code assemblies into files containing native machine instructions before the application is executed, relieving the .NET JIT (Just-In-Time) compiler from having to generate the native instructions at application runtime.</span></span>  

<span data-ttu-id="5bc93-108">在版本6之前，EF 运行时的核心库是 .NET Framework 的一部分，并且会为其自动生成本机映像。</span><span class="sxs-lookup"><span data-stu-id="5bc93-108">Prior to version 6, the EF runtime’s core libraries were part of the .NET Framework and native images were generated automatically for them.</span></span> <span data-ttu-id="5bc93-109">从版本6开始，整个 EF 运行时已合并到 EntityFramework NuGet 包中。</span><span class="sxs-lookup"><span data-stu-id="5bc93-109">Starting with version 6 the whole EF runtime has been combined into the EntityFramework NuGet package.</span></span> <span data-ttu-id="5bc93-110">本机映像现在必须使用 NGen.exe 命令行工具生成，才能获得类似的结果。</span><span class="sxs-lookup"><span data-stu-id="5bc93-110">Native images have to now be generated using the NGen.exe command line tool to obtain similar results.</span></span>  

<span data-ttu-id="5bc93-111">经验观察显示 EF 运行时程序集的本机映像可以在应用程序启动时间的1到3秒之间缩短。</span><span class="sxs-lookup"><span data-stu-id="5bc93-111">Empirical observations show that native images of the EF runtime assemblies can cut between 1 and 3 seconds of application startup time.</span></span>  

## <a name="how-to-use-ngenexe"></a><span data-ttu-id="5bc93-112">如何使用 NGen.exe</span><span class="sxs-lookup"><span data-stu-id="5bc93-112">How to use NGen.exe</span></span>  

<span data-ttu-id="5bc93-113">NGen.exe 工具的最基本功能是 "安装" (即，为程序集及其所有直接依赖项创建和保留到磁盘) 本机映像。</span><span class="sxs-lookup"><span data-stu-id="5bc93-113">The most basic function of the NGen.exe tool is to “install” (that is, to create and persist to disk) native images for an assembly and all its direct dependencies.</span></span> <span data-ttu-id="5bc93-114">下面介绍如何实现此目的：</span><span class="sxs-lookup"><span data-stu-id="5bc93-114">Here is how you can achieve that:</span></span>  

1. <span data-ttu-id="5bc93-115">以管理员身份打开“命令提示符”窗口。</span><span class="sxs-lookup"><span data-stu-id="5bc93-115">Open a Command Prompt window as an administrator.</span></span>
2. <span data-ttu-id="5bc93-116">将当前工作目录更改为你要为其生成本机映像的程序集的位置：</span><span class="sxs-lookup"><span data-stu-id="5bc93-116">Change the current working directory to the location of the assemblies you want to generate native images for:</span></span>

   ``` console
   cd <*Assemblies location*>  
   ```

3. <span data-ttu-id="5bc93-117">根据你的操作系统和应用程序的配置，可能需要为32位体系结构、64位体系结构或两者生成本机映像。</span><span class="sxs-lookup"><span data-stu-id="5bc93-117">Depending on your operating system and the application’s configuration you might need to generate native images for 32 bit architecture, 64 bit architecture or for both.</span></span>

   <span data-ttu-id="5bc93-118">对于32位运行：</span><span class="sxs-lookup"><span data-stu-id="5bc93-118">For 32 bit run:</span></span>

   ``` console
   %WINDIR%\Microsoft.NET\Framework\v4.0.30319\ngen install <Assembly name>  
   ```

   <span data-ttu-id="5bc93-119">对于64位运行：</span><span class="sxs-lookup"><span data-stu-id="5bc93-119">For 64 bit run:</span></span>
  
   ``` console
   %WINDIR%\Microsoft.NET\Framework64\v4.0.30319\ngen install <Assembly name>  
   ```

> [!TIP]
> <span data-ttu-id="5bc93-120">生成错误体系结构的本机映像是一个非常常见的错误。</span><span class="sxs-lookup"><span data-stu-id="5bc93-120">Generating native images for the wrong architecture is a very common mistake.</span></span> <span data-ttu-id="5bc93-121">如果有疑问，只需为适用于计算机中安装的操作系统的所有体系结构生成本机映像。</span><span class="sxs-lookup"><span data-stu-id="5bc93-121">When in doubt you can simply generate native images for all the architectures that apply to the operating system installed in the machine.</span></span>  

<span data-ttu-id="5bc93-122">NGen.exe 还支持其他功能，例如卸载和显示安装的本机映像、对多个映像的生成进行排队等。有关用法的详细信息，请阅读 [NGen.exe 文档](https://msdn.microsoft.com/library/6t9t5wcf.aspx)。</span><span class="sxs-lookup"><span data-stu-id="5bc93-122">NGen.exe also supports other functions such as uninstalling and displaying the installed native images, queuing the generation of multiple images, etc. For more details of usage read the [NGen.exe documentation](https://msdn.microsoft.com/library/6t9t5wcf.aspx).</span></span>  

## <a name="when-to-use-ngenexe"></a><span data-ttu-id="5bc93-123">何时使用 NGen.exe</span><span class="sxs-lookup"><span data-stu-id="5bc93-123">When to use NGen.exe</span></span>  

<span data-ttu-id="5bc93-124">当决定在基于 EF 版本6或更高版本的应用程序中为哪些程序集生成本机映像时，应考虑以下选项：</span><span class="sxs-lookup"><span data-stu-id="5bc93-124">When it comes to decide which assemblies to generate native images for in an application based on EF version 6 or greater, you should consider the following options:</span></span>  

- <span data-ttu-id="5bc93-125">**主要 EF 运行时程序集，EntityFramework.dll**：基于 EF 的典型应用程序在启动时或在首次访问数据库时执行此程序集中的大量代码。</span><span class="sxs-lookup"><span data-stu-id="5bc93-125">**The main EF runtime assembly, EntityFramework.dll**: A typical EF based application executes a significant amount of code from this assembly on startup or on its first access to the database.</span></span> <span data-ttu-id="5bc93-126">因此，创建此程序集的本机映像将产生最大的启动性能提升。</span><span class="sxs-lookup"><span data-stu-id="5bc93-126">Consequently, creating native images of this assembly will produce the biggest gains in startup performance.</span></span>  
- <span data-ttu-id="5bc93-127">**应用程序使用的任何 EF 提供程序程序集**：启动时间也可能与生成这些的本机映像稍微有利。</span><span class="sxs-lookup"><span data-stu-id="5bc93-127">**Any EF provider assembly used by your application**: Startup time can also benefit slightly from generating native images of these.</span></span> <span data-ttu-id="5bc93-128">例如，如果应用程序使用 EF 提供程序进行 SQL Server 则需要为 EntityFramework.SqlServer.dll 生成本机映像。</span><span class="sxs-lookup"><span data-stu-id="5bc93-128">For example, if the application uses the EF provider for SQL Server you will want to generate a native image for EntityFramework.SqlServer.dll.</span></span>  
- <span data-ttu-id="5bc93-129">**应用程序的程序集和其他依赖项**： [NGen.exe 文档](https://msdn.microsoft.com/library/6t9t5wcf.aspx) 涵盖了用于选择要为其生成本机映像的程序集以及本机映像对安全性的影响、高级选项（如 "硬绑定"、在调试和分析方案中使用本机映像等）的一般准则。</span><span class="sxs-lookup"><span data-stu-id="5bc93-129">**Your application’s assemblies and other dependencies**: The [NGen.exe documentation](https://msdn.microsoft.com/library/6t9t5wcf.aspx) covers general criteria for choosing which assemblies to generate native images for and the impact of native images on security, advanced options such as “hard binding”, scenarios such as using native images in debugging and profiling scenarios, etc.</span></span>  

> [!TIP]
> <span data-ttu-id="5bc93-130">请确保仔细衡量对应用程序的启动性能和总体性能的使用本机映像的影响，并将其与实际要求进行比较。</span><span class="sxs-lookup"><span data-stu-id="5bc93-130">Make sure you carefully measure the impact of using native images on both the startup performance and the overall performance of your application and compare them against actual requirements.</span></span> <span data-ttu-id="5bc93-131">尽管本机映像通常有助于改善启动性能，但在某些情况下，会降低内存使用率，并非所有方案都将同样受益。</span><span class="sxs-lookup"><span data-stu-id="5bc93-131">While native images will generally help improve startup up performance and in some cases reduce memory usage, not all scenarios will benefit equally.</span></span> <span data-ttu-id="5bc93-132">例如，在稳定状态执行 (即，一旦调用了应用程序所用的所有方法一次，则) JIT 编译器生成的代码实际上会产生比本机映像更好的性能。</span><span class="sxs-lookup"><span data-stu-id="5bc93-132">For instance, on steady state execution (that is, once all the methods being used by the application have been invoked at least once) code generated by the JIT compiler can in fact yield slightly better performance than native images.</span></span>  

## <a name="using-ngenexe-in-a-development-machine"></a><span data-ttu-id="5bc93-133">在开发计算机中使用 NGen.exe</span><span class="sxs-lookup"><span data-stu-id="5bc93-133">Using NGen.exe in a development machine</span></span>  

<span data-ttu-id="5bc93-134">在开发期间，.NET JIT 编译器将为经常更改的代码提供最佳总体平衡。</span><span class="sxs-lookup"><span data-stu-id="5bc93-134">During development the .NET JIT compiler will offer the best overall tradeoff for code that is changing frequently.</span></span> <span data-ttu-id="5bc93-135">对于已编译的依赖项（如 EF 运行时程序集），生成本机映像可通过在每次执行开始时缩短几秒钟来帮助加速开发和测试。</span><span class="sxs-lookup"><span data-stu-id="5bc93-135">Generating native images for compiled dependencies such as the EF runtime assemblies can help accelerate development and testing by cutting a few seconds out at the beginning of each execution.</span></span>  

<span data-ttu-id="5bc93-136">查找 EF 运行时程序集的好地方是解决方案的 NuGet 包位置。</span><span class="sxs-lookup"><span data-stu-id="5bc93-136">A good place to find the EF runtime assemblies is the NuGet package location for the solution.</span></span> <span data-ttu-id="5bc93-137">例如，对于使用具有 SQL Server 的 EF 6.0.2 并面向 .NET 4.5 或更高版本的应用程序，你可以在命令提示符窗口中键入以下内容， (记得以管理员身份打开它) ：</span><span class="sxs-lookup"><span data-stu-id="5bc93-137">For example, for an application using EF 6.0.2 with SQL Server and targeting .NET 4.5 or greater you can type the following in a Command Prompt window (remember to open it as an administrator):</span></span>  

```console
cd <Solution directory>\packages\EntityFramework.6.0.2\lib\net45
%WINDIR%\Microsoft.NET\Framework\v4.0.30319\ngen install EntityFramework.SqlServer.dll
%WINDIR%\Microsoft.NET\Framework64\v4.0.30319\ngen install EntityFramework.SqlServer.dll
```  

> [!NOTE]
> <span data-ttu-id="5bc93-138">这就充分利用了为 EF 提供程序安装本机映像 SQL Server 的事实，在默认情况下，还会安装主要 EF 运行时程序集的本机映像。</span><span class="sxs-lookup"><span data-stu-id="5bc93-138">This takes advantage of the fact that installing the native images for EF provider for SQL Server will also by default install the native images for the main EF runtime assembly.</span></span> <span data-ttu-id="5bc93-139">这是因为 NGen.exe 可以检测到 EntityFramework.dll 是位于同一目录中的 EntityFramework.SqlServer.dll 程序集的直接依赖项。</span><span class="sxs-lookup"><span data-stu-id="5bc93-139">This works because NGen.exe can detect that EntityFramework.dll is a direct dependency of the EntityFramework.SqlServer.dll assembly located in the same directory.</span></span>  

## <a name="creating-native-images-during-setup"></a><span data-ttu-id="5bc93-140">在安装过程中创建本机映像</span><span class="sxs-lookup"><span data-stu-id="5bc93-140">Creating native images during setup</span></span>  

<span data-ttu-id="5bc93-141">在安装过程中，WiX 工具包支持为托管程序集生成本机映像的排队，如本操作 [方法指南](https://wixtoolset.org/documentation/manual/v3/howtos/files_and_registry/ngen_managed_assemblies.html)中所述。</span><span class="sxs-lookup"><span data-stu-id="5bc93-141">The WiX Toolkit supports queuing the generation of native images for managed assemblies during setup, as explained in this [how-to guide](https://wixtoolset.org/documentation/manual/v3/howtos/files_and_registry/ngen_managed_assemblies.html).</span></span> <span data-ttu-id="5bc93-142">另一种方法是创建执行 NGen.exe 命令的自定义安装任务。</span><span class="sxs-lookup"><span data-stu-id="5bc93-142">Another alternative is to create a custom setup task that execute the NGen.exe command.</span></span>  

## <a name="verifying-that-native-images-are-being-used-for-ef"></a><span data-ttu-id="5bc93-143">正在验证是否正在将本机映像用于 EF</span><span class="sxs-lookup"><span data-stu-id="5bc93-143">Verifying that native images are being used for EF</span></span>  

<span data-ttu-id="5bc93-144">可以通过查找扩展名为 ".ni.dll" 或 ".ni.exe" 的加载程序集来验证特定应用程序是否正在使用本机程序集。</span><span class="sxs-lookup"><span data-stu-id="5bc93-144">You can verify that a specific application is using a native assembly by looking for loaded assemblies that have the extension “.ni.dll” or “.ni.exe”.</span></span> <span data-ttu-id="5bc93-145">例如，EF 的主运行时程序集的本机映像将 EntityFramework.ni.dll 调用。</span><span class="sxs-lookup"><span data-stu-id="5bc93-145">For example, a native image for the EF’s main runtime assembly will be called EntityFramework.ni.dll.</span></span> <span data-ttu-id="5bc93-146">检查进程的加载的 .NET 程序集的一种简单方法是使用 [进程资源管理器](https://technet.microsoft.com/sysinternals/bb896653)。</span><span class="sxs-lookup"><span data-stu-id="5bc93-146">An easy way to inspect the loaded .NET assemblies of a process is to use [Process Explorer](https://technet.microsoft.com/sysinternals/bb896653).</span></span>  

## <a name="other-things-to-be-aware-of"></a><span data-ttu-id="5bc93-147">其他需要注意的问题</span><span class="sxs-lookup"><span data-stu-id="5bc93-147">Other things to be aware of</span></span>  

<span data-ttu-id="5bc93-148">\*\*创建程序集的本机映像不应与在 GAC 中注册程序集[ (全局程序集缓存) ](https://msdn.microsoft.com/library/yf1d93sz.aspx) \*\*。</span><span class="sxs-lookup"><span data-stu-id="5bc93-148">**Creating a native image of an assembly should not be confused with registering the assembly in the [GAC (Global Assembly Cache)](https://msdn.microsoft.com/library/yf1d93sz.aspx)**.</span></span> <span data-ttu-id="5bc93-149">NGen.exe 允许创建不在 GAC 中的程序集的映像，事实上，使用特定 EF 版本的多个应用程序可以共享同一本机映像。</span><span class="sxs-lookup"><span data-stu-id="5bc93-149">NGen.exe allows creating images of assemblies that are not in the GAC, and in fact, multiple applications that use a specific version of EF can share the same native image.</span></span> <span data-ttu-id="5bc93-150">Windows 8 可以自动为 GAC 中放置的程序集创建本机映像，而是将 EF 运行时优化为与应用程序一起部署，并且我们不建议在 GAC 中注册它，因为这样会对程序集解析产生负面影响，并在其他方面提供应用程序的服务。</span><span class="sxs-lookup"><span data-stu-id="5bc93-150">While Windows 8 can automatically create native images for assemblies placed in the GAC, the EF runtime is optimized to be deployed alongside your application and we do not recommend registering it in the GAC as this has a negative impact on assembly resolution and servicing your applications among other aspects.</span></span>  
