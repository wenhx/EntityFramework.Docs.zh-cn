---
title: 升级到实体框架 6-EF6
description: 升级到实体框架6
author: divega
ms.date: 10/23/2016
ms.assetid: 29958ae5-85d3-4585-9ba6-550b8ec9393a
uid: ef6/what-is-new/upgrading-to-ef6
ms.openlocfilehash: b27504e3b18c668a1c41142b9cd5e697944cb5fa
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619708"
---
# <a name="upgrading-to-entity-framework-6"></a><span data-ttu-id="4cb35-103">升级到实体框架6</span><span class="sxs-lookup"><span data-stu-id="4cb35-103">Upgrading to Entity Framework 6</span></span>

<span data-ttu-id="4cb35-104">在以前版本的 EF 中，代码是在核心库之间拆分的 (主要 System.Data.Entity.dll) 作为 .NET Framework 和带外 (OOB) 库的一部分提供， (主要 EntityFramework.dll 在 NuGet 包中提供的) 。</span><span class="sxs-lookup"><span data-stu-id="4cb35-104">In previous versions of EF the code was split between core libraries (primarily System.Data.Entity.dll) shipped as part of the .NET Framework and out-of-band (OOB) libraries (primarily EntityFramework.dll) shipped in a NuGet package.</span></span> <span data-ttu-id="4cb35-105">EF6 采用核心库中的代码，并将其合并到 OOB 库中。</span><span class="sxs-lookup"><span data-stu-id="4cb35-105">EF6 takes the code from the core libraries and incorporates it into the OOB libraries.</span></span> <span data-ttu-id="4cb35-106">此操作是必需的，以便使 EF 成为开源，使其能够以不同于 .NET Framework 的步调发展。</span><span class="sxs-lookup"><span data-stu-id="4cb35-106">This was necessary in order to allow EF to be made open source and for it to be able to evolve at a different pace from .NET Framework.</span></span> <span data-ttu-id="4cb35-107">这样做的结果是需要针对已移动的类型重新生成应用程序。</span><span class="sxs-lookup"><span data-stu-id="4cb35-107">The consequence of this is that applications will need to be rebuilt against the moved types.</span></span>

<span data-ttu-id="4cb35-108">对于使用 EF 4.1 和更高版本中随附的 DbContext 的应用程序，这应该非常简单。</span><span class="sxs-lookup"><span data-stu-id="4cb35-108">This should be straightforward for applications that make use of DbContext as shipped in EF 4.1 and later.</span></span> <span data-ttu-id="4cb35-109">对于使用 ObjectContext 但仍不难执行的应用程序，还需要执行其他一些工作。</span><span class="sxs-lookup"><span data-stu-id="4cb35-109">A little more work is required for applications that make use of ObjectContext but it still isn’t hard to do.</span></span>

<span data-ttu-id="4cb35-110">下面是将现有应用程序升级到 EF6 所需执行的操作清单。</span><span class="sxs-lookup"><span data-stu-id="4cb35-110">Here is a checklist of the things you need to do to upgrade an existing application to EF6.</span></span>

## <a name="1-install-the-ef6-nuget-package"></a><span data-ttu-id="4cb35-111">1. 安装 EF6 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="4cb35-111">1. Install the EF6 NuGet package</span></span>

<span data-ttu-id="4cb35-112">需要升级到新的实体框架6运行时。</span><span class="sxs-lookup"><span data-stu-id="4cb35-112">You need to upgrade to the new Entity Framework 6 runtime.</span></span>

1. <span data-ttu-id="4cb35-113">右键单击项目，然后选择 "**管理 NuGet 包 ...** "</span><span class="sxs-lookup"><span data-stu-id="4cb35-113">Right-click on your project and select **Manage NuGet Packages...**</span></span>  
2. <span data-ttu-id="4cb35-114">在 "**联机**" 选项卡中选择**EntityFramework** ，然后单击 "**安装**"</span><span class="sxs-lookup"><span data-stu-id="4cb35-114">Under the **Online** tab select **EntityFramework** and click **Install**</span></span>  
   > [!NOTE]
   > <span data-ttu-id="4cb35-115">如果安装了 EntityFramework NuGet 包的以前版本，则会将其升级到 EF6。</span><span class="sxs-lookup"><span data-stu-id="4cb35-115">If a previous version of the EntityFramework NuGet package was installed this will upgrade it to EF6.</span></span>

<span data-ttu-id="4cb35-116">或者，你可以从包管理器控制台运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="4cb35-116">Alternatively, you can run the following command from Package Manager Console:</span></span>

``` powershell
Install-Package EntityFramework
```

## <a name="2-ensure-that-assembly-references-to-systemdataentitydll-are-removed"></a><span data-ttu-id="4cb35-117">2. 确保删除对 System.Data.Entity.dll 的程序集引用</span><span class="sxs-lookup"><span data-stu-id="4cb35-117">2. Ensure that assembly references to System.Data.Entity.dll are removed</span></span>

<span data-ttu-id="4cb35-118">安装 EF6 NuGet 包应会自动从你的项目中删除对 System.object 的任何引用。</span><span class="sxs-lookup"><span data-stu-id="4cb35-118">Installing the EF6 NuGet package should automatically remove any references to System.Data.Entity from your project for you.</span></span>

## <a name="3-swap-any-ef-designer-edmx-models-to-use-ef-6x-code-generation"></a><span data-ttu-id="4cb35-119">3. 将任何 EF 设计器替换 (EDMX) 模型以使用 EF 1.x 代码生成</span><span class="sxs-lookup"><span data-stu-id="4cb35-119">3. Swap any EF Designer (EDMX) models to use EF 6.x code generation</span></span>

<span data-ttu-id="4cb35-120">如果使用 EF 设计器创建了任何模型，则需要更新代码生成模板以生成 EF6 兼容代码。</span><span class="sxs-lookup"><span data-stu-id="4cb35-120">If you have any models created with the EF Designer, you will need to update the code generation templates to generate EF6 compatible code.</span></span>

> [!NOTE]
> <span data-ttu-id="4cb35-121">目前只有适用于 Visual Studio 2012 和2013的 EF 1.x DbContext 生成器模板。</span><span class="sxs-lookup"><span data-stu-id="4cb35-121">There are currently only EF 6.x DbContext Generator templates available for Visual Studio 2012 and 2013.</span></span>

1. <span data-ttu-id="4cb35-122">删除现有代码生成模板。</span><span class="sxs-lookup"><span data-stu-id="4cb35-122">Delete existing code-generation templates.</span></span> <span data-ttu-id="4cb35-123">这些文件通常名为\*\* \<edmx_file_name\> . tt**和** \<edmx_file_name\> 。Context.tt\*\* ，并将其嵌套在解决方案资源管理器中的 edmx 文件下。</span><span class="sxs-lookup"><span data-stu-id="4cb35-123">These files will typically be named **\<edmx_file_name\>.tt** and **\<edmx_file_name\>.Context.tt** and be nested under your edmx file in Solution Explorer.</span></span> <span data-ttu-id="4cb35-124">可以在解决方案资源管理器中选择模板，然后按 **Del** 键将它们删除。</span><span class="sxs-lookup"><span data-stu-id="4cb35-124">You can select the templates in Solution Explorer and press the **Del** key to delete them.</span></span>  
   > [!NOTE]
   > <span data-ttu-id="4cb35-125">在网站项目中，模板不会嵌套在 edmx 文件下，但会在解决方案资源管理器中列出。</span><span class="sxs-lookup"><span data-stu-id="4cb35-125">In Web Site projects the templates will not be nested under your edmx file, but listed alongside it in Solution Explorer.</span></span>  

   > [!NOTE]
   > <span data-ttu-id="4cb35-126">在 VB.NET 项目中，你将需要启用 "显示所有文件" 才能查看嵌套的模板文件。</span><span class="sxs-lookup"><span data-stu-id="4cb35-126">In VB.NET projects you will need to enable 'Show All Files' to be able to see the nested template files.</span></span>
2. <span data-ttu-id="4cb35-127">添加适当的 EF 1.x 代码生成模板。</span><span class="sxs-lookup"><span data-stu-id="4cb35-127">Add the appropriate EF 6.x code generation template.</span></span> <span data-ttu-id="4cb35-128">在 EF 设计器中打开模型，右键单击设计图面，然后选择 "**添加代码生成项 ...** "</span><span class="sxs-lookup"><span data-stu-id="4cb35-128">Open your model in the EF Designer, right-click on the design surface and select **Add Code Generation Item...**</span></span>
    - <span data-ttu-id="4cb35-129">如果使用的是 DbContext API (建议) 则在 "**数据**" 选项卡下将提供**EF 1.x DbContext 生成器**。</span><span class="sxs-lookup"><span data-stu-id="4cb35-129">If you are using the DbContext API (recommended) then **EF 6.x DbContext Generator** will be available under the **Data** tab.</span></span>  
      > [!NOTE]
      > <span data-ttu-id="4cb35-130">如果使用的是 Visual Studio 2012，则需要安装 EF 6 工具才能使用此模板。</span><span class="sxs-lookup"><span data-stu-id="4cb35-130">If you are using Visual Studio 2012, you will need to install the EF 6 Tools to have this template.</span></span> <span data-ttu-id="4cb35-131">有关详细信息，请参阅 [获取实体框架](xref:ef6/fundamentals/install) 。</span><span class="sxs-lookup"><span data-stu-id="4cb35-131">See [Get Entity Framework](xref:ef6/fundamentals/install) for details.</span></span>  

    - <span data-ttu-id="4cb35-132">如果你使用的是 ObjectContext API，则需要选择 " **联机** " 选项卡，然后搜索 **EF EntityObject 生成器**。</span><span class="sxs-lookup"><span data-stu-id="4cb35-132">If you are using the ObjectContext API then you will need to select the **Online** tab and search for **EF 6.x EntityObject Generator**.</span></span>  
3. <span data-ttu-id="4cb35-133">如果对代码生成模板应用了任何自定义，则需要将其重新应用到更新的模板。</span><span class="sxs-lookup"><span data-stu-id="4cb35-133">If you applied any customizations to the code generation templates you will need to re-apply them to the updated templates.</span></span>

## <a name="4-update-namespaces-for-any-core-ef-types-being-used"></a><span data-ttu-id="4cb35-134">4. 更新正在使用的任何 core EF 类型的命名空间</span><span class="sxs-lookup"><span data-stu-id="4cb35-134">4. Update namespaces for any core EF types being used</span></span>

<span data-ttu-id="4cb35-135">DbContext 和 Code First 类型的命名空间尚未更改。</span><span class="sxs-lookup"><span data-stu-id="4cb35-135">The namespaces for DbContext and Code First types have not changed.</span></span> <span data-ttu-id="4cb35-136">这意味着，对于使用 EF 4.1 或更高版本的许多应用程序，你无需更改任何内容。</span><span class="sxs-lookup"><span data-stu-id="4cb35-136">This means for many applications that use EF 4.1 or later you will not need to change anything.</span></span>

<span data-ttu-id="4cb35-137">之前在 System.Data.Entity.dll 中的类型（如 ObjectContext）已移动到新命名空间。</span><span class="sxs-lookup"><span data-stu-id="4cb35-137">Types like ObjectContext that were previously in System.Data.Entity.dll have been moved to new namespaces.</span></span> <span data-ttu-id="4cb35-138">这意味着你可能需要更新 *使用* 或 *导入* 指令，以针对 EF6 进行构建。</span><span class="sxs-lookup"><span data-stu-id="4cb35-138">This means you may need to update your *using* or *Import* directives to build against EF6.</span></span>

<span data-ttu-id="4cb35-139">命名空间更改的一般规则是将 System.web. \* 中的任何类型都移到 "system.string"。</span><span class="sxs-lookup"><span data-stu-id="4cb35-139">The general rule for namespace changes is that any type in System.Data.\* is moved to System.Data.Entity.Core.\*.</span></span> <span data-ttu-id="4cb35-140">换言之，只需插入 **Entity。**</span><span class="sxs-lookup"><span data-stu-id="4cb35-140">In other words, just insert **Entity.Core.**</span></span> <span data-ttu-id="4cb35-141">在 System.object 之后。</span><span class="sxs-lookup"><span data-stu-id="4cb35-141">after System.Data.</span></span> <span data-ttu-id="4cb35-142">例如：</span><span class="sxs-lookup"><span data-stu-id="4cb35-142">For example:</span></span>

- <span data-ttu-id="4cb35-143">EntityException => System.object。**Entity。** EntityException</span><span class="sxs-lookup"><span data-stu-id="4cb35-143">System.Data.EntityException => System.Data.**Entity.Core**.EntityException</span></span>  
- <span data-ttu-id="4cb35-144">System.web. ObjectContext => System.object。**Entity。** 对象。 ObjectContext</span><span class="sxs-lookup"><span data-stu-id="4cb35-144">System.Data.Objects.ObjectContext => System.Data.**Entity.Core**.Objects.ObjectContext</span></span>  
- <span data-ttu-id="4cb35-145">Dataclasses.dll. RelationshipManager => 的数据。**Entity。** Dataclasses.dll. RelationshipManager</span><span class="sxs-lookup"><span data-stu-id="4cb35-145">System.Data.Objects.DataClasses.RelationshipManager => System.Data.**Entity.Core**.Objects.DataClasses.RelationshipManager</span></span>  

<span data-ttu-id="4cb35-146">这些类型位于 *核心* 命名空间中，因为它们不能直接用于大多数基于 DbContext 的应用程序。</span><span class="sxs-lookup"><span data-stu-id="4cb35-146">These types are in the *Core* namespaces because they are not used directly for most DbContext-based applications.</span></span> <span data-ttu-id="4cb35-147">作为 System.Data.Entity.dll 的一部分的某些类型仍通常会直接用于基于 DbContext 的应用程序，因此不会移动到 *核心* 命名空间中。</span><span class="sxs-lookup"><span data-stu-id="4cb35-147">Some types that were part of System.Data.Entity.dll are still used commonly and directly for DbContext-based applications and so have not been moved into the *Core* namespaces.</span></span> <span data-ttu-id="4cb35-148">它们是：</span><span class="sxs-lookup"><span data-stu-id="4cb35-148">These are:</span></span>

- <span data-ttu-id="4cb35-149">EntityState => System.object。**实体**。EntityState</span><span class="sxs-lookup"><span data-stu-id="4cb35-149">System.Data.EntityState => System.Data.**Entity**.EntityState</span></span>  
- <span data-ttu-id="4cb35-150">Dataclasses.dll. EdmFunctionAttribute => 的数据。**DbFunctionAttribute**</span><span class="sxs-lookup"><span data-stu-id="4cb35-150">System.Data.Objects.DataClasses.EdmFunctionAttribute => System.Data.**Entity.DbFunctionAttribute**</span></span>  
  > [!NOTE]
  > <span data-ttu-id="4cb35-151">此类已重命名;具有旧名称的类仍然存在并且工作正常，但现在标记为过时。</span><span class="sxs-lookup"><span data-stu-id="4cb35-151">This class has been renamed; a class with the old name still exists and works, but it now marked as obsolete.</span></span>  
- <span data-ttu-id="4cb35-152">EntityFunctions = =>。**DbFunctions**</span><span class="sxs-lookup"><span data-stu-id="4cb35-152">System.Data.Objects.EntityFunctions => System.Data.**Entity.DbFunctions**</span></span>  
  > [!NOTE]
  > <span data-ttu-id="4cb35-153">此类已重命名;具有旧名称的类仍然存在并且工作正常，但现在标记为过时。 ) </span><span class="sxs-lookup"><span data-stu-id="4cb35-153">This class has been renamed; a class with the old name still exists and works, but it now marked as obsolete.)</span></span>  
- <span data-ttu-id="4cb35-154">空间类 (例如，DbGeography、DbGeometry) 已从 System.web => 系统移动。**实体**。空间</span><span class="sxs-lookup"><span data-stu-id="4cb35-154">Spatial classes (for example, DbGeography, DbGeometry) have moved from System.Data.Spatial => System.Data.**Entity**.Spatial</span></span>

> [!NOTE]
> <span data-ttu-id="4cb35-155">System.web 命名空间中的某些类型不是 EF 程序集 System.Data.dll。</span><span class="sxs-lookup"><span data-stu-id="4cb35-155">Some types in the System.Data namespace are in System.Data.dll which is not an EF assembly.</span></span> <span data-ttu-id="4cb35-156">这些类型不会移动，因此它们的命名空间保持不变。</span><span class="sxs-lookup"><span data-stu-id="4cb35-156">These types have not moved and so their namespaces remain unchanged.</span></span>
