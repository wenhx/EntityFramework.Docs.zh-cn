---
title: 每个模型多个关系图-EF6
description: 实体框架6中的每个模型多个关系图
author: divega
ms.date: 10/23/2016
ms.assetid: b95db5c8-de8d-43bd-9ccc-5df6a5e25e1b
uid: ef6/modeling/designer/multiple-diagrams
ms.openlocfilehash: 5ecda72af6430222864c62aea24cf85893436f56
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620432"
---
# <a name="multiple-diagrams-per-model"></a><span data-ttu-id="8e10e-103">每个模型多个关系图</span><span class="sxs-lookup"><span data-stu-id="8e10e-103">Multiple Diagrams per Model</span></span>
> [!NOTE]
> <span data-ttu-id="8e10e-104">**EF5 仅向前** -实体框架5中引入了本页中所述的功能、api 等。</span><span class="sxs-lookup"><span data-stu-id="8e10e-104">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="8e10e-105">如果使用的是早期版本，则部分或全部信息不适用。</span><span class="sxs-lookup"><span data-stu-id="8e10e-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="8e10e-106">此视频和页面显示了如何使用 Entity Framework Designer (EF 设计器) 将模型拆分为多个关系图。</span><span class="sxs-lookup"><span data-stu-id="8e10e-106">This video and page shows how to split a model into multiple diagrams using the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="8e10e-107">当模型变得太大，无法查看或编辑时，可能需要使用此功能。</span><span class="sxs-lookup"><span data-stu-id="8e10e-107">You might want to use this feature when your model becomes too large to view or edit.</span></span>

<span data-ttu-id="8e10e-108">在 EF 设计器的早期版本中，每个 EDMX 文件只能有一个关系图。</span><span class="sxs-lookup"><span data-stu-id="8e10e-108">In earlier versions of the EF Designer you could only have one diagram per the EDMX file.</span></span> <span data-ttu-id="8e10e-109">从 Visual Studio 2012 开始，可以使用 EF 设计器将 EDMX 文件拆分成多个关系图。</span><span class="sxs-lookup"><span data-stu-id="8e10e-109">Starting with Visual Studio 2012, you can use the EF Designer to split your EDMX file into multiple diagrams.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="8e10e-110">观看视频</span><span class="sxs-lookup"><span data-stu-id="8e10e-110">Watch the video</span></span>
<span data-ttu-id="8e10e-111">此视频演示如何使用 Entity Framework Designer (EF 设计器) 将模型拆分为多个关系图。</span><span class="sxs-lookup"><span data-stu-id="8e10e-111">This video shows how to split a model into multiple diagrams using the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="8e10e-112">当模型变得太大，无法查看或编辑时，可能需要使用此功能。</span><span class="sxs-lookup"><span data-stu-id="8e10e-112">You might want to use this feature when your model becomes too large to view or edit.</span></span>

<span data-ttu-id="8e10e-113">**提供者**： Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="8e10e-113">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="8e10e-114">**视频**： [wmv](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.wmv)  |  [.wmv](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-mp4video-multiplediagrams.m4v)  |  [wmv (ZIP) ](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.zip)</span><span class="sxs-lookup"><span data-stu-id="8e10e-114">**Video**: [WMV](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.wmv) | [MP4](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-mp4video-multiplediagrams.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.zip)</span></span>

## <a name="ef-designer-overview"></a><span data-ttu-id="8e10e-115">EF 设计器概述</span><span class="sxs-lookup"><span data-stu-id="8e10e-115">EF Designer Overview</span></span>

<span data-ttu-id="8e10e-116">使用 EF 设计器的实体数据模型向导创建模型时，将创建一个 .edmx 文件并将其添加到解决方案中。</span><span class="sxs-lookup"><span data-stu-id="8e10e-116">When you create a model using the EF Designer’s Entity Data Model Wizard, an .edmx file is created and added to your solution.</span></span> <span data-ttu-id="8e10e-117">此文件定义了你的实体的形状，以及它们如何映射到数据库。</span><span class="sxs-lookup"><span data-stu-id="8e10e-117">This file defines the shape of your entities and how they map to the database.</span></span>

<span data-ttu-id="8e10e-118">EF 设计器包含以下组件：</span><span class="sxs-lookup"><span data-stu-id="8e10e-118">The EF Designer consists of the following components:</span></span>

-   <span data-ttu-id="8e10e-119">用于编辑模型的可视化设计图面。</span><span class="sxs-lookup"><span data-stu-id="8e10e-119">A visual design surface for editing the model.</span></span> <span data-ttu-id="8e10e-120">您可以创建、修改或删除实体和关联。</span><span class="sxs-lookup"><span data-stu-id="8e10e-120">You can create, modify, or delete entities and associations.</span></span>
-   <span data-ttu-id="8e10e-121">提供模型的树视图的 **模型浏览器**   窗口。</span><span class="sxs-lookup"><span data-stu-id="8e10e-121">A **Model Browser** window that provides tree views of the model.</span></span><span data-ttu-id="8e10e-122">实体及其关联位于\* \[ ModelName \] \*文件夹下。</span><span class="sxs-lookup"><span data-stu-id="8e10e-122">  The entities and their associations are located under the *\[ModelName\]* folder.</span></span> <span data-ttu-id="8e10e-123">数据库表和约束位于\* \[ ModelName \] \*下。存储文件夹。</span><span class="sxs-lookup"><span data-stu-id="8e10e-123">The database tables and constraints are located under the *\[ModelName\]*.Store folder.</span></span>
-   <span data-ttu-id="8e10e-124">用于查看和编辑映射的 " **映射详细信息** " 窗口。</span><span class="sxs-lookup"><span data-stu-id="8e10e-124">A **Mapping Details** window for viewing and editing mappings.</span></span> <span data-ttu-id="8e10e-125">您可以将实体类型或关联映射到数据库表、列和存储过程。</span><span class="sxs-lookup"><span data-stu-id="8e10e-125">You can map entity types or associations to database tables, columns, and stored procedures.</span></span> 

<span data-ttu-id="8e10e-126">实体数据模型向导完成时，将自动打开 "可视化设计图面" 窗口。</span><span class="sxs-lookup"><span data-stu-id="8e10e-126">The visual design surface window is automatically opened when the Entity Data Model Wizard finishes.</span></span> <span data-ttu-id="8e10e-127">如果 "模型浏览器" 不可见，请右键单击主设计图面，然后选择 " **模型浏览器**"。</span><span class="sxs-lookup"><span data-stu-id="8e10e-127">If the Model Browser is not visible, right-click the main design surface and select **Model Browser**.</span></span>

<span data-ttu-id="8e10e-128">以下屏幕截图显示了在 EF 设计器中打开的 .edmx 文件。</span><span class="sxs-lookup"><span data-stu-id="8e10e-128">The following screenshot shows an .edmx file opened in the EF Designer.</span></span> <span data-ttu-id="8e10e-129">屏幕截图显示左侧)  (的可视化设计图面，而 **模型浏览器**   窗口 (向右) 。</span><span class="sxs-lookup"><span data-stu-id="8e10e-129">The screenshot shows the visual design surface (to the left) and the **Model Browser** window (to the right).</span></span>

![EF 设计器2](~/ef6/media/efdesigner2.png)

<span data-ttu-id="8e10e-131">若要撤消在 EF 设计器中完成的操作，请单击 "Ctrl + Z"。</span><span class="sxs-lookup"><span data-stu-id="8e10e-131">To undo an operation done in the EF Designer, click Ctrl-Z.</span></span>

## <a name="working-with-diagrams"></a><span data-ttu-id="8e10e-132">使用关系图</span><span class="sxs-lookup"><span data-stu-id="8e10e-132">Working with Diagrams</span></span>

<span data-ttu-id="8e10e-133">默认情况下，EF 设计器会创建一个名为 Diagram1 的关系图。</span><span class="sxs-lookup"><span data-stu-id="8e10e-133">By default the EF Designer creates one diagram called Diagram1.</span></span> <span data-ttu-id="8e10e-134">如果你有一个具有大量实体和关联的关系图，则你最喜欢按逻辑拆分它们。</span><span class="sxs-lookup"><span data-stu-id="8e10e-134">If you have a diagram with a large number of entities and associations, you will most like want to split them up logically.</span></span> <span data-ttu-id="8e10e-135">从 Visual Studio 2012 开始，可以在多个关系图中查看概念模型。</span><span class="sxs-lookup"><span data-stu-id="8e10e-135">Starting with Visual Studio 2012, you can view your conceptual model in multiple diagrams.</span></span>   

<span data-ttu-id="8e10e-136">添加新关系图时，它们将显示在 "模型浏览器" 窗口中的 "关系图" 文件夹下。</span><span class="sxs-lookup"><span data-stu-id="8e10e-136">As you add new diagrams, they appear under the Diagrams folder in the Model Browser window.</span></span> <span data-ttu-id="8e10e-137">重命名关系图：在 "模型浏览器" 窗口中选择关系图，单击 "名称"，然后键入新名称。</span><span class="sxs-lookup"><span data-stu-id="8e10e-137">To rename a diagram: select the diagram in the Model Browser window, click once on the name, and type the new name.</span></span> <span data-ttu-id="8e10e-138">您还可以右键单击关系图名称，然后选择 " **重命名**"。</span><span class="sxs-lookup"><span data-stu-id="8e10e-138"> You can also right-click the diagram name and select **Rename**.</span></span>

<span data-ttu-id="8e10e-139">关系图名称将显示在 Visual Studio 编辑器中的 .edmx 文件名旁。</span><span class="sxs-lookup"><span data-stu-id="8e10e-139">The diagram name is displayed next to the .edmx file name, in the Visual Studio editor.</span></span> <span data-ttu-id="8e10e-140">例如 Model1 \[ Diagram1 \] 。</span><span class="sxs-lookup"><span data-stu-id="8e10e-140">For example Model1.edmx\[Diagram1\].</span></span>

![关系图名称](~/ef6/media/diagramname.png)

<span data-ttu-id="8e10e-142">关系图内容 (实体和关联的形状和颜色) 存储在 .edmx 文件中。</span><span class="sxs-lookup"><span data-stu-id="8e10e-142">The diagrams content (shape and color of entities and associations) is stored in the .edmx.diagram file.</span></span> <span data-ttu-id="8e10e-143">若要查看此文件，请选择解决方案资源管理器并展开 .edmx 文件。</span><span class="sxs-lookup"><span data-stu-id="8e10e-143">To view this file, select Solution Explorer and unfold the .edmx file.</span></span> 

![关系图文件](~/ef6/media/diagramfiles.png)

<span data-ttu-id="8e10e-145">不应手动编辑 .edmx 文件，此文件的内容可能会被 EF 设计器覆盖。</span><span class="sxs-lookup"><span data-stu-id="8e10e-145">You should not edit the .edmx.diagram file manually, the content of this file maybe overwritten by the EF Designer.</span></span>
 
## <a name="splitting-entities-and-associations-into-a-new-diagram"></a><span data-ttu-id="8e10e-146">将实体和关联拆分为新关系图</span><span class="sxs-lookup"><span data-stu-id="8e10e-146">Splitting Entities and Associations into a New Diagram</span></span>

<span data-ttu-id="8e10e-147">您可以在现有关系图上选择实体 (按住 Shift 来选择多个实体) 。</span><span class="sxs-lookup"><span data-stu-id="8e10e-147">You can select entities on the existing diagram (hold Shift to select multiple entities).</span></span> <span data-ttu-id="8e10e-148">单击鼠标右键并选择 " **移到新关系图**"。</span><span class="sxs-lookup"><span data-stu-id="8e10e-148">Click the right mouse button and select **Move to new Diagram**.</span></span> <span data-ttu-id="8e10e-149">将创建新关系图，并将所选实体及其关联移动到关系图中。</span><span class="sxs-lookup"><span data-stu-id="8e10e-149">The new diagram is created and the selected entities and their associations are moved to the diagram.</span></span>

<span data-ttu-id="8e10e-150">或者，您可以在模型浏览器中右键单击 "关系图" 文件夹，然后选择 " **添加新关系图"。**</span><span class="sxs-lookup"><span data-stu-id="8e10e-150">Alternatively, you can right-click the Diagrams folder in Model Browser and select **Add new Diagram.**</span></span> <span data-ttu-id="8e10e-151">然后，可以将实体从模型浏览器中的 "实体类型" 文件夹下拖放到设计图面上。</span><span class="sxs-lookup"><span data-stu-id="8e10e-151">You can then drag and drop entities from under the Entity Types folder in Model Browser onto the design surface.</span></span>

<span data-ttu-id="8e10e-152">你还可以使用 Ctrl X 或 Ctrl-c 键) 从一个关系图中剪切或复制 (实体，然后使用 Ctrl + V 键) 在另一个关系图中粘贴 (。</span><span class="sxs-lookup"><span data-stu-id="8e10e-152">You can also cut or copy entities (using Ctrl-X or Ctrl-C keys) from one diagram and paste (using Ctrl-V key) on the other.</span></span> <span data-ttu-id="8e10e-153">如果要将实体粘贴到其中的关系图已经包含具有相同名称的实体，则将创建一个新实体并将其添加到模型中。</span><span class="sxs-lookup"><span data-stu-id="8e10e-153">If the diagram into which you are pasting an entity already contains an entity with the same name, a new entity will be created and added to the model.</span></span><span data-ttu-id="8e10e-154">例如： Diagram2 包含 "部门" 实体。</span><span class="sxs-lookup"><span data-stu-id="8e10e-154">  For example: Diagram2 contains the Department entity.</span></span> <span data-ttu-id="8e10e-155">然后，将其他部门粘贴到 Diagram2 上。</span><span class="sxs-lookup"><span data-stu-id="8e10e-155">Then, you paste another Department on Diagram2.</span></span> <span data-ttu-id="8e10e-156">将创建 Department1 实体并将其添加到概念模型中。</span><span class="sxs-lookup"><span data-stu-id="8e10e-156">The Department1 entity is created and added to the conceptual model.</span></span>   

<span data-ttu-id="8e10e-157">若要在关系图中包含相关实体，则 rick-单击该实体并选择 " **包括相关**项"。</span><span class="sxs-lookup"><span data-stu-id="8e10e-157">To include related entities in a diagram, rick-click the entity and select **Include Related**.</span></span> <span data-ttu-id="8e10e-158">这将在指定的关系图中创建相关实体和关联的副本。</span><span class="sxs-lookup"><span data-stu-id="8e10e-158">This will make a copy of the related entities and associations in the specified diagram.</span></span>

## <a name="changing-the-color-of-entities"></a><span data-ttu-id="8e10e-159">更改实体的颜色</span><span class="sxs-lookup"><span data-stu-id="8e10e-159">Changing the Color of Entities</span></span>

<span data-ttu-id="8e10e-160">除了将模型拆分为多个关系图外，还可以更改实体的颜色。</span><span class="sxs-lookup"><span data-stu-id="8e10e-160">In addition to splitting a model into multiple diagrams, you can also change colors of your entities.</span></span>

<span data-ttu-id="8e10e-161">若要更改颜色，请在设计图面上选择一个实体 (或多个实体) 。</span><span class="sxs-lookup"><span data-stu-id="8e10e-161">To change the color, select an entity (or multiple entities) on the design surface.</span></span> <span data-ttu-id="8e10e-162">然后，单击鼠标右键并选择 " **属性**"。</span><span class="sxs-lookup"><span data-stu-id="8e10e-162">Then, click the right mouse button and select **Properties**.</span></span> <span data-ttu-id="8e10e-163">在属性窗口中，选择 " **填充颜色** " 属性。</span><span class="sxs-lookup"><span data-stu-id="8e10e-163">In the Properties window, select the **Fill Color** property.</span></span> <span data-ttu-id="8e10e-164">使用有效的颜色名称指定颜色 (例如，红色) 或有效的 RGB (例如，255、128、128) 。</span><span class="sxs-lookup"><span data-stu-id="8e10e-164">Specify the color using either a valid color name (for example, Red) or a valid RGB (for example, 255, 128, 128).</span></span> 

![更改颜色](~/ef6/media/color.png)

## <a name="summary"></a><span data-ttu-id="8e10e-166">总结</span><span class="sxs-lookup"><span data-stu-id="8e10e-166">Summary</span></span>

<span data-ttu-id="8e10e-167">在本主题中，我们介绍如何将模型拆分为多个关系图，以及如何使用 Entity Framework Designer 为实体指定不同的颜色。</span><span class="sxs-lookup"><span data-stu-id="8e10e-167">In this topic we looked at how to split a model into multiple diagrams and also how to specify a different color for an entity using the Entity Framework Designer.</span></span> 
