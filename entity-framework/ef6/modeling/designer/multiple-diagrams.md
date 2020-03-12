---
title: 每个模型多个关系图-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: b95db5c8-de8d-43bd-9ccc-5df6a5e25e1b
ms.openlocfilehash: e78b927a147143629ac5b73e23edf439ba6d0264
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415117"
---
# <a name="multiple-diagrams-per-model"></a><span data-ttu-id="1b916-102">每个模型多个关系图</span><span class="sxs-lookup"><span data-stu-id="1b916-102">Multiple Diagrams per Model</span></span>
> [!NOTE]
> <span data-ttu-id="1b916-103">**EF5 仅向前**-实体框架5中引入了本页中所述的功能、api 等。</span><span class="sxs-lookup"><span data-stu-id="1b916-103">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="1b916-104">如果使用的是早期版本，则部分或全部信息不适用。</span><span class="sxs-lookup"><span data-stu-id="1b916-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="1b916-105">此视频和页面显示了如何使用 Entity Framework Designer （EF 设计器）将模型拆分为多个关系图。</span><span class="sxs-lookup"><span data-stu-id="1b916-105">This video and page shows how to split a model into multiple diagrams using the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="1b916-106">当模型变得太大，无法查看或编辑时，可能需要使用此功能。</span><span class="sxs-lookup"><span data-stu-id="1b916-106">You might want to use this feature when your model becomes too large to view or edit.</span></span>

<span data-ttu-id="1b916-107">在 EF 设计器的早期版本中，每个 EDMX 文件只能有一个关系图。</span><span class="sxs-lookup"><span data-stu-id="1b916-107">In earlier versions of the EF Designer you could only have one diagram per the EDMX file.</span></span> <span data-ttu-id="1b916-108">从 Visual Studio 2012 开始，可以使用 EF 设计器将 EDMX 文件拆分成多个关系图。</span><span class="sxs-lookup"><span data-stu-id="1b916-108">Starting with Visual Studio 2012, you can use the EF Designer to split your EDMX file into multiple diagrams.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="1b916-109">观看视频</span><span class="sxs-lookup"><span data-stu-id="1b916-109">Watch the video</span></span>
<span data-ttu-id="1b916-110">此视频演示如何使用 Entity Framework Designer （EF 设计器）将模型拆分为多个关系图。</span><span class="sxs-lookup"><span data-stu-id="1b916-110">This video shows how to split a model into multiple diagrams using the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="1b916-111">当模型变得太大，无法查看或编辑时，可能需要使用此功能。</span><span class="sxs-lookup"><span data-stu-id="1b916-111">You might want to use this feature when your model becomes too large to view or edit.</span></span>

<span data-ttu-id="1b916-112">**主讲人**：Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="1b916-112">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="1b916-113">**视频**：WMV | [MP4](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-mp4video-multiplediagrams.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.zip)</span><span class="sxs-lookup"><span data-stu-id="1b916-113">**Video**: [WMV](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.wmv) | [MP4](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-mp4video-multiplediagrams.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.zip)</span></span>

## <a name="ef-designer-overview"></a><span data-ttu-id="1b916-114">EF 设计器概述</span><span class="sxs-lookup"><span data-stu-id="1b916-114">EF Designer Overview</span></span>

<span data-ttu-id="1b916-115">使用 EF 设计器的实体数据模型向导创建模型时，将创建一个 .edmx 文件并将其添加到解决方案中。</span><span class="sxs-lookup"><span data-stu-id="1b916-115">When you create a model using the EF Designer’s Entity Data Model Wizard, an .edmx file is created and added to your solution.</span></span> <span data-ttu-id="1b916-116">此文件定义了你的实体的形状，以及它们如何映射到数据库。</span><span class="sxs-lookup"><span data-stu-id="1b916-116">This file defines the shape of your entities and how they map to the database.</span></span>

<span data-ttu-id="1b916-117">EF 设计器包含以下组件：</span><span class="sxs-lookup"><span data-stu-id="1b916-117">The EF Designer consists of the following components:</span></span>

-   <span data-ttu-id="1b916-118">用于编辑模型的可视化设计图面。</span><span class="sxs-lookup"><span data-stu-id="1b916-118">A visual design surface for editing the model.</span></span> <span data-ttu-id="1b916-119">您可以创建、修改或删除实体和关联。</span><span class="sxs-lookup"><span data-stu-id="1b916-119">You can create, modify, or delete entities and associations.</span></span>
-   <span data-ttu-id="1b916-120"> **模型浏览器** 窗口提供模型的树视图。</span><span class="sxs-lookup"><span data-stu-id="1b916-120">A **Model Browser** window that provides tree views of the model.</span></span><span data-ttu-id="1b916-121">  实体及其关联位于 *\[ModelName\]* 文件夹下。</span><span class="sxs-lookup"><span data-stu-id="1b916-121">  The entities and their associations are located under the *\[ModelName\]* folder.</span></span> <span data-ttu-id="1b916-122">数据库表和约束位于 *\[ModelName\]* 下。存储文件夹。</span><span class="sxs-lookup"><span data-stu-id="1b916-122">The database tables and constraints are located under the *\[ModelName\]*.Store folder.</span></span>
-   <span data-ttu-id="1b916-123">用于查看和编辑映射的 " **映射详细信息**" 窗口。</span><span class="sxs-lookup"><span data-stu-id="1b916-123">A **Mapping Details** window for viewing and editing mappings.</span></span> <span data-ttu-id="1b916-124">您可以将实体类型或关联映射到数据库表、列和存储过程。</span><span class="sxs-lookup"><span data-stu-id="1b916-124">You can map entity types or associations to database tables, columns, and stored procedures.</span></span> 

<span data-ttu-id="1b916-125">实体数据模型向导完成时，将自动打开 "可视化设计图面" 窗口。</span><span class="sxs-lookup"><span data-stu-id="1b916-125">The visual design surface window is automatically opened when the Entity Data Model Wizard finishes.</span></span> <span data-ttu-id="1b916-126">如果 "模型浏览器" 不可见，请右键单击主设计图面，然后选择 " **模型浏览器**"。</span><span class="sxs-lookup"><span data-stu-id="1b916-126">If the Model Browser is not visible, right-click the main design surface and select **Model Browser**.</span></span>

<span data-ttu-id="1b916-127">以下屏幕截图显示了在 EF 设计器中打开的 .edmx 文件。</span><span class="sxs-lookup"><span data-stu-id="1b916-127">The following screenshot shows an .edmx file opened in the EF Designer.</span></span> <span data-ttu-id="1b916-128">屏幕截图显示可视化设计图面（左侧）和 **模型浏览器** 窗口（右侧）。</span><span class="sxs-lookup"><span data-stu-id="1b916-128">The screenshot shows the visual design surface (to the left) and the **Model Browser** window (to the right).</span></span>

![EF 设计器2](~/ef6/media/efdesigner2.png)

<span data-ttu-id="1b916-130">若要撤消在 EF 设计器中完成的操作，请单击 "Ctrl + Z"。</span><span class="sxs-lookup"><span data-stu-id="1b916-130">To undo an operation done in the EF Designer, click Ctrl-Z.</span></span>

## <a name="working-with-diagrams"></a><span data-ttu-id="1b916-131">使用关系图</span><span class="sxs-lookup"><span data-stu-id="1b916-131">Working with Diagrams</span></span>

<span data-ttu-id="1b916-132">默认情况下，EF 设计器会创建一个名为 Diagram1 的关系图。</span><span class="sxs-lookup"><span data-stu-id="1b916-132">By default the EF Designer creates one diagram called Diagram1.</span></span> <span data-ttu-id="1b916-133">如果你有一个具有大量实体和关联的关系图，则你最喜欢按逻辑拆分它们。</span><span class="sxs-lookup"><span data-stu-id="1b916-133">If you have a diagram with a large number of entities and associations, you will most like want to split them up logically.</span></span> <span data-ttu-id="1b916-134">从 Visual Studio 2012 开始，可以在多个关系图中查看概念模型。</span><span class="sxs-lookup"><span data-stu-id="1b916-134">Starting with Visual Studio 2012, you can view your conceptual model in multiple diagrams.</span></span>   

<span data-ttu-id="1b916-135">添加新关系图时，它们将显示在 "模型浏览器" 窗口中的 "关系图" 文件夹下。</span><span class="sxs-lookup"><span data-stu-id="1b916-135">As you add new diagrams, they appear under the Diagrams folder in the Model Browser window.</span></span> <span data-ttu-id="1b916-136">重命名关系图：在 "模型浏览器" 窗口中选择关系图，单击 "名称"，然后键入新名称。</span><span class="sxs-lookup"><span data-stu-id="1b916-136">To rename a diagram: select the diagram in the Model Browser window, click once on the name, and type the new name.</span></span> <span data-ttu-id="1b916-137"> 您还可以右键单击关系图名称，然后选择 "**重命名**"。</span><span class="sxs-lookup"><span data-stu-id="1b916-137"> You can also right-click the diagram name and select **Rename**.</span></span>

<span data-ttu-id="1b916-138">关系图名称将显示在 Visual Studio 编辑器中的 .edmx 文件名旁。</span><span class="sxs-lookup"><span data-stu-id="1b916-138">The diagram name is displayed next to the .edmx file name, in the Visual Studio editor.</span></span> <span data-ttu-id="1b916-139">例如，Model1\[Diagram1\]。</span><span class="sxs-lookup"><span data-stu-id="1b916-139">For example Model1.edmx\[Diagram1\].</span></span>

![DiagramName](~/ef6/media/diagramname.png)

<span data-ttu-id="1b916-141">关系图内容（实体和关联的形状和颜色）存储在 .edmx 文件中。</span><span class="sxs-lookup"><span data-stu-id="1b916-141">The diagrams content (shape and color of entities and associations) is stored in the .edmx.diagram file.</span></span> <span data-ttu-id="1b916-142">若要查看此文件，请选择解决方案资源管理器并展开 .edmx 文件。</span><span class="sxs-lookup"><span data-stu-id="1b916-142">To view this file, select Solution Explorer and unfold the .edmx file.</span></span> 

![DiagramFiles](~/ef6/media/diagramfiles.png)

<span data-ttu-id="1b916-144">不应手动编辑 .edmx 文件，此文件的内容可能会被 EF 设计器覆盖。</span><span class="sxs-lookup"><span data-stu-id="1b916-144">You should not edit the .edmx.diagram file manually, the content of this file maybe overwritten by the EF Designer.</span></span>
 
## <a name="splitting-entities-and-associations-into-a-new-diagram"></a><span data-ttu-id="1b916-145">将实体和关联拆分为新关系图</span><span class="sxs-lookup"><span data-stu-id="1b916-145">Splitting Entities and Associations into a New Diagram</span></span>

<span data-ttu-id="1b916-146">您可以选择现有关系图上的实体（按住 Shift 键以选择多个实体）。</span><span class="sxs-lookup"><span data-stu-id="1b916-146">You can select entities on the existing diagram (hold Shift to select multiple entities).</span></span> <span data-ttu-id="1b916-147">单击鼠标右键并选择 "**移到新关系图**"。</span><span class="sxs-lookup"><span data-stu-id="1b916-147">Click the right mouse button and select **Move to new Diagram**.</span></span> <span data-ttu-id="1b916-148">将创建新关系图，并将所选实体及其关联移动到关系图中。</span><span class="sxs-lookup"><span data-stu-id="1b916-148">The new diagram is created and the selected entities and their associations are moved to the diagram.</span></span>

<span data-ttu-id="1b916-149">或者，您可以在模型浏览器中右键单击 "关系图" 文件夹，然后选择 "**添加新关系图"。**</span><span class="sxs-lookup"><span data-stu-id="1b916-149">Alternatively, you can right-click the Diagrams folder in Model Browser and select **Add new Diagram.**</span></span> <span data-ttu-id="1b916-150">然后，可以将实体从模型浏览器中的 "实体类型" 文件夹下拖放到设计图面上。</span><span class="sxs-lookup"><span data-stu-id="1b916-150">You can then drag and drop entities from under the Entity Types folder in Model Browser onto the design surface.</span></span>

<span data-ttu-id="1b916-151">您还可以从一个关系图中剪切或复制实体（使用 Ctrl + X 或 Ctrl + C 键），然后在另一个关系图中粘贴（使用 Ctrl + V 键）。</span><span class="sxs-lookup"><span data-stu-id="1b916-151">You can also cut or copy entities (using Ctrl-X or Ctrl-C keys) from one diagram and paste (using Ctrl-V key) on the other.</span></span> <span data-ttu-id="1b916-152">如果要将实体粘贴到其中的关系图已经包含具有相同名称的实体，则将创建一个新实体并将其添加到模型中。</span><span class="sxs-lookup"><span data-stu-id="1b916-152">If the diagram into which you are pasting an entity already contains an entity with the same name, a new entity will be created and added to the model.</span></span><span data-ttu-id="1b916-153">  例如：Diagram2 包含部门实体。</span><span class="sxs-lookup"><span data-stu-id="1b916-153">  For example: Diagram2 contains the Department entity.</span></span> <span data-ttu-id="1b916-154">然后，将其他部门粘贴到 Diagram2 上。</span><span class="sxs-lookup"><span data-stu-id="1b916-154">Then, you paste another Department on Diagram2.</span></span> <span data-ttu-id="1b916-155">将创建 Department1 实体并将其添加到概念模型中。</span><span class="sxs-lookup"><span data-stu-id="1b916-155">The Department1 entity is created and added to the conceptual model.</span></span>   

<span data-ttu-id="1b916-156">若要在关系图中包含相关实体，则 rick-单击该实体并选择 "**包括相关**项"。</span><span class="sxs-lookup"><span data-stu-id="1b916-156">To include related entities in a diagram, rick-click the entity and select **Include Related**.</span></span> <span data-ttu-id="1b916-157">这将在指定的关系图中创建相关实体和关联的副本。</span><span class="sxs-lookup"><span data-stu-id="1b916-157">This will make a copy of the related entities and associations in the specified diagram.</span></span>

## <a name="changing-the-color-of-entities"></a><span data-ttu-id="1b916-158">更改实体的颜色</span><span class="sxs-lookup"><span data-stu-id="1b916-158">Changing the Color of Entities</span></span>

<span data-ttu-id="1b916-159">除了将模型拆分为多个关系图外，还可以更改实体的颜色。</span><span class="sxs-lookup"><span data-stu-id="1b916-159">In addition to splitting a model into multiple diagrams, you can also change colors of your entities.</span></span>

<span data-ttu-id="1b916-160">若要更改颜色，请在设计图面上选择一个或多个实体。</span><span class="sxs-lookup"><span data-stu-id="1b916-160">To change the color, select an entity (or multiple entities) on the design surface.</span></span> <span data-ttu-id="1b916-161">然后，单击鼠标右键并选择 "**属性**"。</span><span class="sxs-lookup"><span data-stu-id="1b916-161">Then, click the right mouse button and select **Properties**.</span></span> <span data-ttu-id="1b916-162">在属性窗口中，选择 "**填充颜色**" 属性。</span><span class="sxs-lookup"><span data-stu-id="1b916-162">In the Properties window, select the **Fill Color** property.</span></span> <span data-ttu-id="1b916-163">使用有效的颜色名称（例如，Red）或有效的 RGB （例如，255、128、128）指定颜色。</span><span class="sxs-lookup"><span data-stu-id="1b916-163">Specify the color using either a valid color name (for example, Red) or a valid RGB (for example, 255, 128, 128).</span></span> 

![颜色](~/ef6/media/color.png)

## <a name="summary"></a><span data-ttu-id="1b916-165">Summary</span><span class="sxs-lookup"><span data-stu-id="1b916-165">Summary</span></span>

<span data-ttu-id="1b916-166">在本主题中，我们介绍如何将模型拆分为多个关系图，以及如何使用 Entity Framework Designer 为实体指定不同的颜色。</span><span class="sxs-lookup"><span data-stu-id="1b916-166">In this topic we looked at how to split a model into multiple diagrams and also how to specify a different color for an entity using the Entity Framework Designer.</span></span> 
