---
title: 选择 EF 设计器模型实体框架运行时版本-EF6
description: 为实体框架6中的 EF 设计器模型选择实体框架运行时版本
author: divega
ms.date: 10/23/2016
ms.openlocfilehash: 2025ca5e46c7d9a38ba596d57c023a2c1b224129
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073261"
---
# <a name="selecting-entity-framework-runtime-version-for-ef-designer-models"></a><span data-ttu-id="92746-103">选择 EF 设计器模型实体框架运行时版本</span><span class="sxs-lookup"><span data-stu-id="92746-103">Selecting Entity Framework Runtime Version for EF Designer Models</span></span>
> [!NOTE]
> <span data-ttu-id="92746-104">**仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。</span><span class="sxs-lookup"><span data-stu-id="92746-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="92746-105">如果使用的是早期版本，则部分或全部信息不适用。</span><span class="sxs-lookup"><span data-stu-id="92746-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="92746-106">从 EF6 开始，将以下屏幕添加到 EF 设计器，以便在创建模型时选择想要面向的运行时版本。</span><span class="sxs-lookup"><span data-stu-id="92746-106">Starting with EF6 the following screen was added to the EF Designer to allow you to select the version of the runtime you wish to target when creating a model.</span></span> <span data-ttu-id="92746-107">当项目中尚未安装最新版本的实体框架时，将显示该屏幕。</span><span class="sxs-lookup"><span data-stu-id="92746-107">The screen will appear when the latest version of Entity Framework is not already installed in the project.</span></span> <span data-ttu-id="92746-108">如果已安装最新版本，则默认情况下将使用它。</span><span class="sxs-lookup"><span data-stu-id="92746-108">If the latest version is already installed it will just be used by default.</span></span>

![选择运行时版本](~/ef6/media/screen.png)

## <a name="targeting-ef6x"></a><span data-ttu-id="92746-110">面向 EF6. x</span><span class="sxs-lookup"><span data-stu-id="92746-110">Targeting EF6.x</span></span>

<span data-ttu-id="92746-111">你可以从 "选择你的版本" 屏幕中选择 "EF6"，将 EF6 运行时添加到你的项目。</span><span class="sxs-lookup"><span data-stu-id="92746-111">You can choose EF6 from the 'Choose Your Version' screen to add the EF6 runtime to your project.</span></span> <span data-ttu-id="92746-112">添加 EF6 后，你将停止在当前项目中看到此屏幕。</span><span class="sxs-lookup"><span data-stu-id="92746-112">Once you've added EF6, you’ll stop seeing this screen in the current project.</span></span>

<span data-ttu-id="92746-113">如果已安装旧版本的 EF，EF6 将被禁用 (因为不能将多个版本的运行时) 的项目作为目标。</span><span class="sxs-lookup"><span data-stu-id="92746-113">EF6 will be disabled if you already have an older version of EF installed (since you can't target multiple versions of the runtime from the same project).</span></span> <span data-ttu-id="92746-114">如果此处未启用 EF6 选项，请按照以下步骤将项目升级到 EF6：</span><span class="sxs-lookup"><span data-stu-id="92746-114">If EF6 option is not enabled here, follow these steps to upgrade your project to EF6:</span></span>

1.  <span data-ttu-id="92746-115">在解决方案资源管理器中右键单击项目，然后选择 "**管理 NuGet 包 ...** "</span><span class="sxs-lookup"><span data-stu-id="92746-115">Right-click on your project in Solution Explorer and select **Manage NuGet Packages...**</span></span>
2.  <span data-ttu-id="92746-116">选择 **更新**</span><span class="sxs-lookup"><span data-stu-id="92746-116">Select **Updates**</span></span>
3.  <span data-ttu-id="92746-117">选择 **EntityFramework** (确保它将更新为所需版本) </span><span class="sxs-lookup"><span data-stu-id="92746-117">Select **EntityFramework** (make sure it is going to update it to the version you want)</span></span>
4.  <span data-ttu-id="92746-118">单击 "**更新**"</span><span class="sxs-lookup"><span data-stu-id="92746-118">Click **Update**</span></span>

 

## <a name="targeting-ef5x"></a><span data-ttu-id="92746-119">面向 EF5. x</span><span class="sxs-lookup"><span data-stu-id="92746-119">Targeting EF5.x</span></span>

<span data-ttu-id="92746-120">你可以从 "选择你的版本" 屏幕中选择 "EF5"，将 EF5 运行时添加到你的项目。</span><span class="sxs-lookup"><span data-stu-id="92746-120">You can choose EF5 from the 'Choose Your Version' screen to add the EF5 runtime to your project.</span></span> <span data-ttu-id="92746-121">添加 EF5 后，仍会看到此屏幕，其中 "EF6" 选项处于禁用状态。</span><span class="sxs-lookup"><span data-stu-id="92746-121">Once you've added EF5, you’ll still see the screen with the EF6 option disabled.</span></span>

<span data-ttu-id="92746-122">如果已安装 EF4 版本的运行时，则会看到屏幕上列出的 EF 版本（而不是 EF5）。</span><span class="sxs-lookup"><span data-stu-id="92746-122">If you have an EF4.x version of the runtime already installed then you will see that version of EF listed in the screen rather than EF5.</span></span> <span data-ttu-id="92746-123">在这种情况下，可以使用以下步骤升级到 EF5：</span><span class="sxs-lookup"><span data-stu-id="92746-123">In this situation you can upgrade to EF5 using the following steps:</span></span>

1.  <span data-ttu-id="92746-124">选择 **工具- &gt; 库包管理器- &gt; 程序包管理器控制台**</span><span class="sxs-lookup"><span data-stu-id="92746-124">Select **Tools -&gt; Library Package Manager -&gt; Package Manager Console**</span></span>
2.  <span data-ttu-id="92746-125">运行 **安装包 EntityFramework-版本 5.0.0**</span><span class="sxs-lookup"><span data-stu-id="92746-125">Run **Install-Package EntityFramework -version 5.0.0**</span></span>

 

## <a name="targeting-ef4x"></a><span data-ttu-id="92746-126">面向 EF4. x</span><span class="sxs-lookup"><span data-stu-id="92746-126">Targeting EF4.x</span></span>

<span data-ttu-id="92746-127">可以使用以下步骤将 EF4 运行时安装到项目中：</span><span class="sxs-lookup"><span data-stu-id="92746-127">You can install the EF4.x runtime to your project using the following steps:</span></span>

1.  <span data-ttu-id="92746-128">选择 **工具- &gt; 库包管理器- &gt; 程序包管理器控制台**</span><span class="sxs-lookup"><span data-stu-id="92746-128">Select **Tools -&gt; Library Package Manager -&gt; Package Manager Console**</span></span>
2.  <span data-ttu-id="92746-129">运行 **安装包 EntityFramework-版本 4.3.0**</span><span class="sxs-lookup"><span data-stu-id="92746-129">Run **Install-Package EntityFramework -version 4.3.0**</span></span>
