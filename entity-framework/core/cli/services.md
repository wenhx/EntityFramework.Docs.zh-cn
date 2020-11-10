---
title: 设计时服务-EF Core
description: Entity Framework Core 设计时服务的信息
author: bricelam
ms.date: 10/22/2020
uid: core/cli/services
ms.openlocfilehash: 07fc8a39fb106f357e135a17f6867ab338621910
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431228"
---
# <a name="design-time-services"></a><span data-ttu-id="28f29-103">设计时服务</span><span class="sxs-lookup"><span data-stu-id="28f29-103">Design-time services</span></span>

<span data-ttu-id="28f29-104">这些工具使用的某些服务仅在设计时使用。</span><span class="sxs-lookup"><span data-stu-id="28f29-104">Some services used by the tools are only used at design time.</span></span> <span data-ttu-id="28f29-105">这些服务独立于 EF Core 的运行时服务进行管理，以防止这些服务与你的应用程序一起部署。</span><span class="sxs-lookup"><span data-stu-id="28f29-105">These services are managed separately from EF Core's runtime services to prevent them from being deployed with your app.</span></span> <span data-ttu-id="28f29-106">若要替代其中某个服务 (例如，服务) 生成迁移文件，请将的实现添加 `IDesignTimeServices` 到启动项目。</span><span class="sxs-lookup"><span data-stu-id="28f29-106">To override one of these services (for example the service to generate migration files), add an implementation of `IDesignTimeServices` to your startup project.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs#DesignTimeServices)]

## <a name="referencing-microsoftentityframeworkcoredesign"></a><span data-ttu-id="28f29-107">引用 Microsoft.entityframeworkcore</span><span class="sxs-lookup"><span data-stu-id="28f29-107">Referencing Microsoft.EntityFrameworkCore.Design</span></span>

<span data-ttu-id="28f29-108">Microsoft.entityframeworkcore 是 DevelopmentDependency 包。</span><span class="sxs-lookup"><span data-stu-id="28f29-108">Microsoft.EntityFrameworkCore.Design is a DevelopmentDependency package.</span></span> <span data-ttu-id="28f29-109">这意味着依赖关系不会以可传递方式流向其他项目，并且你不能在默认情况下引用其类型。</span><span class="sxs-lookup"><span data-stu-id="28f29-109">This means that the dependency won't flow transitively into other projects, and that you cannot, by default, reference its types.</span></span>

<span data-ttu-id="28f29-110">为了引用其类型并重写设计时服务，请在项目文件中更新 PackageReference 项的元数据。</span><span class="sxs-lookup"><span data-stu-id="28f29-110">In order to reference its types and override design-time services, update the PackageReference item's metadata in your project file.</span></span>

```xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.1.9">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

<span data-ttu-id="28f29-111">如果正在通过 Microsoft.entityframeworkcore 以可传递方式对包进行引用，则需要将显式 PackageReference 添加到包并更改其元数据。</span><span class="sxs-lookup"><span data-stu-id="28f29-111">If the package is being referenced transitively via Microsoft.EntityFrameworkCore.Tools, you will need to add an explicit PackageReference to the package and change its metadata.</span></span>

## <a name="list-of-services"></a><span data-ttu-id="28f29-112">服务列表</span><span class="sxs-lookup"><span data-stu-id="28f29-112">List of services</span></span>

<span data-ttu-id="28f29-113">下面是设计时服务的列表。</span><span class="sxs-lookup"><span data-stu-id="28f29-113">The following is a list of the design-time services.</span></span>

<span data-ttu-id="28f29-114">服务</span><span class="sxs-lookup"><span data-stu-id="28f29-114">Service</span></span>                                                                              | <span data-ttu-id="28f29-115">说明</span><span class="sxs-lookup"><span data-stu-id="28f29-115">Description</span></span>
------------------------------------------------------------------------------------ | -----------
<xref:Microsoft.EntityFrameworkCore.Design.IAnnotationCodeGenerator>                 | <span data-ttu-id="28f29-116">为相应的模型批注生成代码。</span><span class="sxs-lookup"><span data-stu-id="28f29-116">Generates the code for corresponding model annotations.</span></span>
<xref:Microsoft.EntityFrameworkCore.Design.ICSharpHelper>                            | <span data-ttu-id="28f29-117">有助于生成 c # 代码。</span><span class="sxs-lookup"><span data-stu-id="28f29-117">Helps with generating C# code.</span></span>
<xref:Microsoft.EntityFrameworkCore.Design.IPluralizer>                              | <span data-ttu-id="28f29-118">为和单数形式单词。</span><span class="sxs-lookup"><span data-stu-id="28f29-118">Pluralizes and singularizes words.</span></span>
<xref:Microsoft.EntityFrameworkCore.Migrations.Design.IMigrationsCodeGenerator>      | <span data-ttu-id="28f29-119">为迁移生成代码。</span><span class="sxs-lookup"><span data-stu-id="28f29-119">Generates code for a migration.</span></span>
<xref:Microsoft.EntityFrameworkCore.Migrations.Design.IMigrationsScaffolder>         | <span data-ttu-id="28f29-120">用于管理迁移文件的主类。</span><span class="sxs-lookup"><span data-stu-id="28f29-120">The main class for managing migration files.</span></span>
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IDatabaseModelFactory>               | <span data-ttu-id="28f29-121">从数据库创建数据库模型。</span><span class="sxs-lookup"><span data-stu-id="28f29-121">Creates a database model from a database.</span></span>
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IModelCodeGenerator>                 | <span data-ttu-id="28f29-122">为模型生成代码。</span><span class="sxs-lookup"><span data-stu-id="28f29-122">Generates code for a model.</span></span>
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IProviderConfigurationCodeGenerator> | <span data-ttu-id="28f29-123">生成 OnConfiguring 代码。</span><span class="sxs-lookup"><span data-stu-id="28f29-123">Generates OnConfiguring code.</span></span>
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IReverseEngineerScaffolder>          | <span data-ttu-id="28f29-124">基架反向工程模型的主类。</span><span class="sxs-lookup"><span data-stu-id="28f29-124">The main class for scaffolding reverse engineered models.</span></span>

## <a name="using-services"></a><span data-ttu-id="28f29-125">使用服务</span><span class="sxs-lookup"><span data-stu-id="28f29-125">Using services</span></span>

<span data-ttu-id="28f29-126">这些服务也可用于创建自己的工具。</span><span class="sxs-lookup"><span data-stu-id="28f29-126">These services can also be useful for creating your own tools.</span></span> <span data-ttu-id="28f29-127">例如，当你想要自动执行部分设计时工作流时。</span><span class="sxs-lookup"><span data-stu-id="28f29-127">For example, when you want to automate part of you design-time workflow.</span></span>

<span data-ttu-id="28f29-128">可以使用 AddEntityFrameworkDesignTimeServices 和 AddDbContextDesignTimeServices 扩展方法生成包含这些服务的服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="28f29-128">You can build a service provider containing these services using the AddEntityFrameworkDesignTimeServices and AddDbContextDesignTimeServices extension methods.</span></span>

[!code-csharp[](../../../samples/core/Miscellaneous/CommandLine/CustomTools.cs#CustomTools)]
