---
title: 升级到 Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 29958ae5-85d3-4585-9ba6-550b8ec9393a
ms.openlocfilehash: 711f1940080de27bd23cb8f641a5c7f2711dd65b
ms.sourcegitcommit: a6082a2caee62029f101eb1000656966195cd6ee
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2018
ms.locfileid: "53182002"
---
# <a name="upgrading-to-entity-framework-6"></a><span data-ttu-id="e3bc8-102">升级到 Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="e3bc8-102">Upgrading to Entity Framework 6</span></span>

<span data-ttu-id="e3bc8-103">在以前版本的 EF 代码已附带的.NET Framework 核心库 (主要 System.Data.Entity.dll 中) 和 NuGet 包中提供的带外 (OOB) 库 (主要 EntityFramework.dll) 之间拆分。</span><span class="sxs-lookup"><span data-stu-id="e3bc8-103">In previous versions of EF the code was split between core libraries (primarily System.Data.Entity.dll) shipped as part of the .NET Framework and out-of-band (OOB) libraries (primarily EntityFramework.dll) shipped in a NuGet package.</span></span> <span data-ttu-id="e3bc8-104">EF6 从核心库中获取代码，并合并到 OOB 库。</span><span class="sxs-lookup"><span data-stu-id="e3bc8-104">EF6 takes the code from the core libraries and incorporates it into the OOB libraries.</span></span> <span data-ttu-id="e3bc8-105">这是必要的以允许 EF 进行开放源代码并使其成为能够从.NET Framework 的不同速度发展。</span><span class="sxs-lookup"><span data-stu-id="e3bc8-105">This was necessary in order to allow EF to be made open source and for it to be able to evolve at a different pace from .NET Framework.</span></span> <span data-ttu-id="e3bc8-106">此结果是应用程序将需要重新生成对已移动的类型。</span><span class="sxs-lookup"><span data-stu-id="e3bc8-106">The consequence of this is that applications will need to be rebuilt against the moved types.</span></span>

<span data-ttu-id="e3bc8-107">这应该是直接使用 DbContext 产品附带 EF 4.1 及更高版本的应用程序。</span><span class="sxs-lookup"><span data-stu-id="e3bc8-107">This should be straightforward for applications that make use of DbContext as shipped in EF 4.1 and later.</span></span> <span data-ttu-id="e3bc8-108">需要执行一些操作是必需的应用程序使用 ObjectContext 的但它仍不难做到。</span><span class="sxs-lookup"><span data-stu-id="e3bc8-108">A little more work is required for applications that make use of ObjectContext but it still isn’t hard to do.</span></span>

<span data-ttu-id="e3bc8-109">以下是你需要如何升级到 EF6 的现有应用程序的内容的清单。</span><span class="sxs-lookup"><span data-stu-id="e3bc8-109">Here is a checklist of the things you need to do to upgrade an existing application to EF6.</span></span>

## <a name="1-install-the-ef6-nuget-package"></a><span data-ttu-id="e3bc8-110">1.安装 EF6 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="e3bc8-110">1. Install the EF6 NuGet package</span></span>

<span data-ttu-id="e3bc8-111">需要升级到新的 Entity Framework 6 运行时。</span><span class="sxs-lookup"><span data-stu-id="e3bc8-111">You need to upgrade to the new Entity Framework 6 runtime.</span></span>

1. <span data-ttu-id="e3bc8-112">右键单击项目并选择**管理 NuGet 包...**</span><span class="sxs-lookup"><span data-stu-id="e3bc8-112">Right-click on your project and select **Manage NuGet Packages...**</span></span>  
2. <span data-ttu-id="e3bc8-113">下**联机**选项卡上选择**EntityFramework**单击**安装**</span><span class="sxs-lookup"><span data-stu-id="e3bc8-113">Under the **Online** tab select **EntityFramework** and click **Install**</span></span>  
   > [!NOTE]
   > <span data-ttu-id="e3bc8-114">如果以前版本的 EntityFramework NuGet 包已安装这会将其升级到 EF6。</span><span class="sxs-lookup"><span data-stu-id="e3bc8-114">If a previous version of the EntityFramework NuGet package was installed this will upgrade it to EF6.</span></span>

<span data-ttu-id="e3bc8-115">或者，您可以从包管理器控制台运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="e3bc8-115">Alternatively, you can run the following command from Package Manager Console:</span></span>

``` powershell
Install-Package EntityFramework
```

## <a name="2-ensure-that-assembly-references-to-systemdataentitydll-are-removed"></a><span data-ttu-id="e3bc8-116">2.请确保删除对 System.Data.Entity.dll 的程序集引用</span><span class="sxs-lookup"><span data-stu-id="e3bc8-116">2. Ensure that assembly references to System.Data.Entity.dll are removed</span></span>

<span data-ttu-id="e3bc8-117">安装 EF6 NuGet 包应自动删除为您对 System.Data.Entity 从你的项目的任何引用。</span><span class="sxs-lookup"><span data-stu-id="e3bc8-117">Installing the EF6 NuGet package should automatically remove any references to System.Data.Entity from your project for you.</span></span>

## <a name="3-swap-any-ef-designer-edmx-models-to-use-ef-6x-code-generation"></a><span data-ttu-id="e3bc8-118">3.交换任何 EF 设计器 (EDMX) 模型以使用 EF 6.x 代码生成</span><span class="sxs-lookup"><span data-stu-id="e3bc8-118">3. Swap any EF Designer (EDMX) models to use EF 6.x code generation</span></span>

<span data-ttu-id="e3bc8-119">如果有使用 EF 设计器创建的任何模型，您需要更新代码生成模板，以生成 EF6 兼容的代码。</span><span class="sxs-lookup"><span data-stu-id="e3bc8-119">If you have any models created with the EF Designer, you will need to update the code generation templates to generate EF6 compatible code.</span></span>

> [!NOTE]
> <span data-ttu-id="e3bc8-120">目前只有 EF 6.x DbContext 生成器模板可用于 Visual Studio 2012 和 2013年。</span><span class="sxs-lookup"><span data-stu-id="e3bc8-120">There are currently only EF 6.x DbContext Generator templates available for Visual Studio 2012 and 2013.</span></span>

1. <span data-ttu-id="e3bc8-121">删除现有的代码生成模板。</span><span class="sxs-lookup"><span data-stu-id="e3bc8-121">Delete existing code-generation templates.</span></span> <span data-ttu-id="e3bc8-122">这些文件通常将叫做 **\<edmx_file_name\>.tt**并 **\<edmx_file_name\>。Context.tt**和嵌套在你在解决方案资源管理器的 edmx 文件下。</span><span class="sxs-lookup"><span data-stu-id="e3bc8-122">These files will typically be named **\<edmx_file_name\>.tt** and **\<edmx_file_name\>.Context.tt** and be nested under your edmx file in Solution Explorer.</span></span> <span data-ttu-id="e3bc8-123">你可以选择模板中，解决方案资源管理器并按**Del**键以将其删除。</span><span class="sxs-lookup"><span data-stu-id="e3bc8-123">You can select the templates in Solution Explorer and press the **Del** key to delete them.</span></span>  
   > [!NOTE]
   > <span data-ttu-id="e3bc8-124">在网站项目模板将不会嵌套在您的 edmx 文件下, 但与其一起列出在解决方案资源管理器。</span><span class="sxs-lookup"><span data-stu-id="e3bc8-124">In Web Site projects the templates will not be nested under your edmx file, but listed alongside it in Solution Explorer.</span></span>  

   > [!NOTE]
   > <span data-ttu-id="e3bc8-125">在 VB.NET 项目中将需要启用显示所有文件若要能够看到嵌套的模板文件。</span><span class="sxs-lookup"><span data-stu-id="e3bc8-125">In VB.NET projects you will need to enable 'Show All Files' to be able to see the nested template files.</span></span>
2. <span data-ttu-id="e3bc8-126">添加适当的 EF 6.x 代码生成模板。</span><span class="sxs-lookup"><span data-stu-id="e3bc8-126">Add the appropriate EF 6.x code generation template.</span></span> <span data-ttu-id="e3bc8-127">打开您的模型在 EF 设计器中，右键单击设计图面，然后选择**添加代码生成项...**</span><span class="sxs-lookup"><span data-stu-id="e3bc8-127">Open your model in the EF Designer, right-click on the design surface and select **Add Code Generation Item...**</span></span>
    - <span data-ttu-id="e3bc8-128">如果使用 DbContext API （推荐），然后**EF 6.x DbContext Generator**均可下**数据**选项卡。</span><span class="sxs-lookup"><span data-stu-id="e3bc8-128">If you are using the DbContext API (recommended) then **EF 6.x DbContext Generator** will be available under the **Data** tab.</span></span>  
      > [!NOTE]
      > <span data-ttu-id="e3bc8-129">如果使用 Visual Studio 2012，你需要安装 EF 6 工具具有此模板。</span><span class="sxs-lookup"><span data-stu-id="e3bc8-129">If you are using Visual Studio 2012, you will need to install the EF 6 Tools to have this template.</span></span> <span data-ttu-id="e3bc8-130">请参阅[获取实体框架](~/ef6/fundamentals/install.md)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="e3bc8-130">See [Get Entity Framework](~/ef6/fundamentals/install.md) for details.</span></span>  

    - <span data-ttu-id="e3bc8-131">如果您使用 ObjectContext API，则将需要选择**联机**选项卡并搜索**EF 6.x EntityObject 生成器**。</span><span class="sxs-lookup"><span data-stu-id="e3bc8-131">If you are using the ObjectContext API then you will need to select the **Online** tab and search for **EF 6.x EntityObject Generator**.</span></span>  
3. <span data-ttu-id="e3bc8-132">如果任何自定义应用于代码生成模板，需要重新将其应用到已更新的模板。</span><span class="sxs-lookup"><span data-stu-id="e3bc8-132">If you applied any customizations to the code generation templates you will need to re-apply them to the updated templates.</span></span>

## <a name="4-update-namespaces-for-any-core-ef-types-being-used"></a><span data-ttu-id="e3bc8-133">4.更新适用于正在使用任何核心 EF 类型命名空间</span><span class="sxs-lookup"><span data-stu-id="e3bc8-133">4. Update namespaces for any core EF types being used</span></span>

<span data-ttu-id="e3bc8-134">未更改 DbContext 和 Code First 类型的命名空间。</span><span class="sxs-lookup"><span data-stu-id="e3bc8-134">The namespaces for DbContext and Code First types have not changed.</span></span> <span data-ttu-id="e3bc8-135">这意味着，对于许多应用程序使用 EF 4.1 或更高版本将不需要进行任何更改。</span><span class="sxs-lookup"><span data-stu-id="e3bc8-135">This means for many applications that use EF 4.1 or later you will not need to change anything.</span></span>

<span data-ttu-id="e3bc8-136">类型，如 ObjectContext 以前在 System.Data.Entity.dll 中都已移到新的命名空间。</span><span class="sxs-lookup"><span data-stu-id="e3bc8-136">Types like ObjectContext that were previously in System.Data.Entity.dll have been moved to new namespaces.</span></span> <span data-ttu-id="e3bc8-137">这意味着可能需要更新您*使用*或*导入*指令以生成针对 EF6。</span><span class="sxs-lookup"><span data-stu-id="e3bc8-137">This means you may need to update your *using* or *Import* directives to build against EF6.</span></span>

<span data-ttu-id="e3bc8-138">命名空间的更改的一般规则是 System.Data.\* 中的任何类型被移动到 System.Data.Entity.Core.\*。</span><span class="sxs-lookup"><span data-stu-id="e3bc8-138">The general rule for namespace changes is that any type in System.Data.\* is moved to System.Data.Entity.Core.\*.</span></span> <span data-ttu-id="e3bc8-139">换而言之，只需插入**Entity.Core。**</span><span class="sxs-lookup"><span data-stu-id="e3bc8-139">In other words, just insert **Entity.Core.**</span></span> <span data-ttu-id="e3bc8-140">之后 System.Data。</span><span class="sxs-lookup"><span data-stu-id="e3bc8-140">after System.Data.</span></span> <span data-ttu-id="e3bc8-141">例如：</span><span class="sxs-lookup"><span data-stu-id="e3bc8-141">For example:</span></span>

- <span data-ttu-id="e3bc8-142">System.Data.EntityException = > System.Data.**Entity.Core**.EntityException</span><span class="sxs-lookup"><span data-stu-id="e3bc8-142">System.Data.EntityException => System.Data.**Entity.Core**.EntityException</span></span>  
- <span data-ttu-id="e3bc8-143">System.Data.Objects.ObjectContext = > System.Data.**Entity.Core**.Objects.ObjectContext</span><span class="sxs-lookup"><span data-stu-id="e3bc8-143">System.Data.Objects.ObjectContext => System.Data.**Entity.Core**.Objects.ObjectContext</span></span>  
- <span data-ttu-id="e3bc8-144">System.Data.Objects.DataClasses.RelationshipManager = > System.Data.**Entity.Core**.Objects.DataClasses.RelationshipManager</span><span class="sxs-lookup"><span data-stu-id="e3bc8-144">System.Data.Objects.DataClasses.RelationshipManager => System.Data.**Entity.Core**.Objects.DataClasses.RelationshipManager</span></span>  

<span data-ttu-id="e3bc8-145">这些类型是在*Core*命名空间因为它们不能直接提供大多数基于 DbContext 的应用程序。</span><span class="sxs-lookup"><span data-stu-id="e3bc8-145">These types are in the *Core* namespaces because they are not used directly for most DbContext-based applications.</span></span> <span data-ttu-id="e3bc8-146">属于 System.Data.Entity.dll 某些类型仍然可用于通常和直接基于 DbContext 的应用程序，因此不已移动到*Core*命名空间。</span><span class="sxs-lookup"><span data-stu-id="e3bc8-146">Some types that were part of System.Data.Entity.dll are still used commonly and directly for DbContext-based applications and so have not been moved into the *Core* namespaces.</span></span> <span data-ttu-id="e3bc8-147">这些是：</span><span class="sxs-lookup"><span data-stu-id="e3bc8-147">These are:</span></span>

- <span data-ttu-id="e3bc8-148">System.Data.EntityState = > System.Data.**实体**.EntityState</span><span class="sxs-lookup"><span data-stu-id="e3bc8-148">System.Data.EntityState => System.Data.**Entity**.EntityState</span></span>  
- <span data-ttu-id="e3bc8-149">System.Data.Objects.DataClasses.EdmFunctionAttribute = > System.Data.**Entity.DbFunctionAttribute**</span><span class="sxs-lookup"><span data-stu-id="e3bc8-149">System.Data.Objects.DataClasses.EdmFunctionAttribute => System.Data.**Entity.DbFunctionAttribute**</span></span>  
  > [!NOTE]
  > <span data-ttu-id="e3bc8-150">此类已重命名;具有旧名称的类仍存在，且工作原理，但它现在标记为已过时。</span><span class="sxs-lookup"><span data-stu-id="e3bc8-150">This class has been renamed; a class with the old name still exists and works, but it now marked as obsolete.</span></span>  
- <span data-ttu-id="e3bc8-151">System.Data.Objects.EntityFunctions = > System.Data.**Entity.DbFunctions**</span><span class="sxs-lookup"><span data-stu-id="e3bc8-151">System.Data.Objects.EntityFunctions => System.Data.**Entity.DbFunctions**</span></span>  
  > [!NOTE]
  > <span data-ttu-id="e3bc8-152">此类已重命名;具有旧名称的类仍存在，并且工作原理，但它现已标记为已过时。）</span><span class="sxs-lookup"><span data-stu-id="e3bc8-152">This class has been renamed; a class with the old name still exists and works, but it now marked as obsolete.)</span></span>  
- <span data-ttu-id="e3bc8-153">空间类 （例如，DbGeography，DbGeometry） 已从 System.Data.Spatial = > System.Data.**实体**.空间</span><span class="sxs-lookup"><span data-stu-id="e3bc8-153">Spatial classes (for example, DbGeography, DbGeometry) have moved from System.Data.Spatial => System.Data.**Entity**.Spatial</span></span>

> [!NOTE]
> <span data-ttu-id="e3bc8-154">System.Data 命名空间中的某些类型是在 System.Data.dll 中这不是 EF 程序集。</span><span class="sxs-lookup"><span data-stu-id="e3bc8-154">Some types in the System.Data namespace are in System.Data.dll which is not an EF assembly.</span></span> <span data-ttu-id="e3bc8-155">未将这些类型，因此它们的命名空间保持不变。</span><span class="sxs-lookup"><span data-stu-id="e3bc8-155">These types have not moved and so their namespaces remain unchanged.</span></span>
