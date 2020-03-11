---
title: 选择 EF 设计器模型实体框架运行时版本-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 7ace90a6-46f8-4f55-a88c-7cad9620085c
ms.openlocfilehash: 40ad05c1f015e6a51150d04eee8d9aa581d72c33
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414991"
---
# <a name="selecting-entity-framework-runtime-version-for-ef-designer-models"></a><span data-ttu-id="f5172-102">选择 EF 设计器模型实体框架运行时版本</span><span class="sxs-lookup"><span data-stu-id="f5172-102">Selecting Entity Framework Runtime Version for EF Designer Models</span></span>
> [!NOTE]
> <span data-ttu-id="f5172-103">**仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。</span><span class="sxs-lookup"><span data-stu-id="f5172-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="f5172-104">如果使用的是早期版本，则部分或全部信息不适用。</span><span class="sxs-lookup"><span data-stu-id="f5172-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="f5172-105">从 EF6 开始，将以下屏幕添加到 EF 设计器，以便在创建模型时选择想要面向的运行时版本。</span><span class="sxs-lookup"><span data-stu-id="f5172-105">Starting with EF6 the following screen was added to the EF Designer to allow you to select the version of the runtime you wish to target when creating a model.</span></span> <span data-ttu-id="f5172-106">当项目中尚未安装最新版本的实体框架时，将显示该屏幕。</span><span class="sxs-lookup"><span data-stu-id="f5172-106">The screen will appear when the latest version of Entity Framework is not already installed in the project.</span></span> <span data-ttu-id="f5172-107">如果已安装最新版本，则默认情况下将使用它。</span><span class="sxs-lookup"><span data-stu-id="f5172-107">If the latest version is already installed it will just be used by default.</span></span>

![屏幕](~/ef6/media/screen.png)


## <a name="targeting-ef6x"></a><span data-ttu-id="f5172-109">面向 EF6. x</span><span class="sxs-lookup"><span data-stu-id="f5172-109">Targeting EF6.x</span></span>

<span data-ttu-id="f5172-110">你可以从 "选择你的版本" 屏幕中选择 "EF6"，将 EF6 运行时添加到你的项目。</span><span class="sxs-lookup"><span data-stu-id="f5172-110">You can choose EF6 from the 'Choose Your Version' screen to add the EF6 runtime to your project.</span></span> <span data-ttu-id="f5172-111">添加 EF6 后，你将停止在当前项目中看到此屏幕。</span><span class="sxs-lookup"><span data-stu-id="f5172-111">Once you've added EF6, you’ll stop seeing this screen in the current project.</span></span>

<span data-ttu-id="f5172-112">如果已安装旧版 EF，则将禁用 EF6 （因为不能将多个版本的运行时作为同一个项目的目标）。</span><span class="sxs-lookup"><span data-stu-id="f5172-112">EF6 will be disabled if you already have an older version of EF installed (since you can't target multiple versions of the runtime from the same project).</span></span> <span data-ttu-id="f5172-113">如果此处未启用 EF6 选项，请按照以下步骤将项目升级到 EF6：</span><span class="sxs-lookup"><span data-stu-id="f5172-113">If EF6 option is not enabled here, follow these steps to upgrade your project to EF6:</span></span>

1.  <span data-ttu-id="f5172-114">在解决方案资源管理器中右键单击项目，然后选择 "**管理 NuGet 包 ...** "</span><span class="sxs-lookup"><span data-stu-id="f5172-114">Right-click on your project in Solution Explorer and select **Manage NuGet Packages...**</span></span>
2.  <span data-ttu-id="f5172-115">选择**更新**</span><span class="sxs-lookup"><span data-stu-id="f5172-115">Select **Updates**</span></span>
3.  <span data-ttu-id="f5172-116">选择**EntityFramework** （确保它将更新为所需的版本）</span><span class="sxs-lookup"><span data-stu-id="f5172-116">Select **EntityFramework** (make sure it is going to update it to the version you want)</span></span>
4.  <span data-ttu-id="f5172-117">单击“更新”。</span><span class="sxs-lookup"><span data-stu-id="f5172-117">Click **Update**</span></span>

 

## <a name="targeting-ef5x"></a><span data-ttu-id="f5172-118">面向 EF5. x</span><span class="sxs-lookup"><span data-stu-id="f5172-118">Targeting EF5.x</span></span>

<span data-ttu-id="f5172-119">你可以从 "选择你的版本" 屏幕中选择 "EF5"，将 EF5 运行时添加到你的项目。</span><span class="sxs-lookup"><span data-stu-id="f5172-119">You can choose EF5 from the 'Choose Your Version' screen to add the EF5 runtime to your project.</span></span> <span data-ttu-id="f5172-120">添加 EF5 后，仍会看到此屏幕，其中 "EF6" 选项处于禁用状态。</span><span class="sxs-lookup"><span data-stu-id="f5172-120">Once you've added EF5, you’ll still see the screen with the EF6 option disabled.</span></span>

<span data-ttu-id="f5172-121">如果已安装 EF4 版本的运行时，则会看到屏幕上列出的 EF 版本（而不是 EF5）。</span><span class="sxs-lookup"><span data-stu-id="f5172-121">If you have an EF4.x version of the runtime already installed then you will see that version of EF listed in the screen rather than EF5.</span></span> <span data-ttu-id="f5172-122">在这种情况下，可以使用以下步骤升级到 EF5：</span><span class="sxs-lookup"><span data-stu-id="f5172-122">In this situation you can upgrade to EF5 using the following steps:</span></span>

1.  <span data-ttu-id="f5172-123">选择**工具-&gt; 库包管理器-&gt; 程序包管理器控制台**</span><span class="sxs-lookup"><span data-stu-id="f5172-123">Select **Tools -&gt; Library Package Manager -&gt; Package Manager Console**</span></span>
2.  <span data-ttu-id="f5172-124">运行**安装包 EntityFramework-版本 5.0.0**</span><span class="sxs-lookup"><span data-stu-id="f5172-124">Run **Install-Package EntityFramework -version 5.0.0**</span></span>

 

## <a name="targeting-ef4x"></a><span data-ttu-id="f5172-125">面向 EF4. x</span><span class="sxs-lookup"><span data-stu-id="f5172-125">Targeting EF4.x</span></span>

<span data-ttu-id="f5172-126">可以使用以下步骤将 EF4 运行时安装到项目中：</span><span class="sxs-lookup"><span data-stu-id="f5172-126">You can install the EF4.x runtime to your project using the following steps:</span></span>

1.  <span data-ttu-id="f5172-127">选择**工具-&gt; 库包管理器-&gt; 程序包管理器控制台**</span><span class="sxs-lookup"><span data-stu-id="f5172-127">Select **Tools -&gt; Library Package Manager -&gt; Package Manager Console**</span></span>
2.  <span data-ttu-id="f5172-128">运行**安装包 EntityFramework-版本 4.3.0**</span><span class="sxs-lookup"><span data-stu-id="f5172-128">Run **Install-Package EntityFramework -version 4.3.0**</span></span>
