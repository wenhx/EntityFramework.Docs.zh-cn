---
title: 每个模型的 EF6 的多个关系图
author: divega
ms.date: 10/23/2016
ms.assetid: b95db5c8-de8d-43bd-9ccc-5df6a5e25e1b
ms.openlocfilehash: e78b927a147143629ac5b73e23edf439ba6d0264
ms.sourcegitcommit: 269c8a1a457a9ad27b4026c22c4b1a76991fb360
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2018
ms.locfileid: "46283610"
---
# <a name="multiple-diagrams-per-model"></a><span data-ttu-id="4d64a-102">每个模型的多个关系图</span><span class="sxs-lookup"><span data-stu-id="4d64a-102">Multiple Diagrams per Model</span></span>
> [!NOTE]
> <span data-ttu-id="4d64a-103">**EF5 及更高版本仅**的功能，Api，Entity Framework 5 中引入了此页所述的等。</span><span class="sxs-lookup"><span data-stu-id="4d64a-103">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="4d64a-104">如果使用的是早期版本，则部分或全部信息不适用。</span><span class="sxs-lookup"><span data-stu-id="4d64a-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="4d64a-105">此视频和页显示了如何将模型分割成多个关系图使用实体框架设计器 （EF 设计器）。</span><span class="sxs-lookup"><span data-stu-id="4d64a-105">This video and page shows how to split a model into multiple diagrams using the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="4d64a-106">您可能想要您的模型变得太大，无法查看或编辑时使用此功能。</span><span class="sxs-lookup"><span data-stu-id="4d64a-106">You might want to use this feature when your model becomes too large to view or edit.</span></span>

<span data-ttu-id="4d64a-107">在早期版本的 EF 设计器可以只让每个 EDMX 文件一个关系图。</span><span class="sxs-lookup"><span data-stu-id="4d64a-107">In earlier versions of the EF Designer you could only have one diagram per the EDMX file.</span></span> <span data-ttu-id="4d64a-108">从 Visual Studio 2012 开始，你可以使用 EF 设计器拆分为多个关系图的 EDMX 文件。</span><span class="sxs-lookup"><span data-stu-id="4d64a-108">Starting with Visual Studio 2012, you can use the EF Designer to split your EDMX file into multiple diagrams.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="4d64a-109">观看视频</span><span class="sxs-lookup"><span data-stu-id="4d64a-109">Watch the video</span></span>
<span data-ttu-id="4d64a-110">此视频介绍如何将模型分割成多个关系图使用实体框架设计器 （EF 设计器）。</span><span class="sxs-lookup"><span data-stu-id="4d64a-110">This video shows how to split a model into multiple diagrams using the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="4d64a-111">您可能想要您的模型变得太大，无法查看或编辑时使用此功能。</span><span class="sxs-lookup"><span data-stu-id="4d64a-111">You might want to use this feature when your model becomes too large to view or edit.</span></span>

<span data-ttu-id="4d64a-112">**主讲人**： 以下 Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="4d64a-112">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="4d64a-113">**视频**: [WMV](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.wmv) | [MP4](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-mp4video-multiplediagrams.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.zip)</span><span class="sxs-lookup"><span data-stu-id="4d64a-113">**Video**: [WMV](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.wmv) | [MP4](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-mp4video-multiplediagrams.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.zip)</span></span>

## <a name="ef-designer-overview"></a><span data-ttu-id="4d64a-114">EF 设计器概述</span><span class="sxs-lookup"><span data-stu-id="4d64a-114">EF Designer Overview</span></span>

<span data-ttu-id="4d64a-115">当您创建使用 EF 设计器的实体数据模型向导的模型时，.edmx 文件被创建并添加到你的解决方案。</span><span class="sxs-lookup"><span data-stu-id="4d64a-115">When you create a model using the EF Designer’s Entity Data Model Wizard, an .edmx file is created and added to your solution.</span></span> <span data-ttu-id="4d64a-116">此文件定义的实体以及它们如何映射到数据库的形状。</span><span class="sxs-lookup"><span data-stu-id="4d64a-116">This file defines the shape of your entities and how they map to the database.</span></span>

<span data-ttu-id="4d64a-117">在 EF 设计器包括以下组件：</span><span class="sxs-lookup"><span data-stu-id="4d64a-117">The EF Designer consists of the following components:</span></span>

-   <span data-ttu-id="4d64a-118">用于编辑的模型的可视化设计图面。</span><span class="sxs-lookup"><span data-stu-id="4d64a-118">A visual design surface for editing the model.</span></span> <span data-ttu-id="4d64a-119">您可以创建、修改或删除实体和关联。</span><span class="sxs-lookup"><span data-stu-id="4d64a-119">You can create, modify, or delete entities and associations.</span></span>
-   <span data-ttu-id="4d64a-120">一个**模型浏览器**提供模型的树视图的窗口。</span><span class="sxs-lookup"><span data-stu-id="4d64a-120">A **Model Browser** window that provides tree views of the model.</span></span>  <span data-ttu-id="4d64a-121">实体和其关联都位于下*\[ModelName\]* 文件夹。</span><span class="sxs-lookup"><span data-stu-id="4d64a-121">The entities and their associations are located under the *\[ModelName\]* folder.</span></span> <span data-ttu-id="4d64a-122">数据库表和约束都位于下 *\[ModelName\]*。将文件夹存储。</span><span class="sxs-lookup"><span data-stu-id="4d64a-122">The database tables and constraints are located under the *\[ModelName\]*.Store folder.</span></span>
-   <span data-ttu-id="4d64a-123">一个**映射详细信息**窗口用于查看和编辑映射。</span><span class="sxs-lookup"><span data-stu-id="4d64a-123">A **Mapping Details** window for viewing and editing mappings.</span></span> <span data-ttu-id="4d64a-124">您可以将实体类型或关联映射到数据库表、列和存储过程。</span><span class="sxs-lookup"><span data-stu-id="4d64a-124">You can map entity types or associations to database tables, columns, and stored procedures.</span></span> 

<span data-ttu-id="4d64a-125">实体数据模型向导完成后，将自动打开可视化设计图面窗口。</span><span class="sxs-lookup"><span data-stu-id="4d64a-125">The visual design surface window is automatically opened when the Entity Data Model Wizard finishes.</span></span> <span data-ttu-id="4d64a-126">如果模型浏览器不可见，请右键单击主设计图面，然后选择**模型浏览器**。</span><span class="sxs-lookup"><span data-stu-id="4d64a-126">If the Model Browser is not visible, right-click the main design surface and select **Model Browser**.</span></span>

<span data-ttu-id="4d64a-127">以下屏幕截图显示了在 EF 设计器中打开.edmx 文件。</span><span class="sxs-lookup"><span data-stu-id="4d64a-127">The following screenshot shows an .edmx file opened in the EF Designer.</span></span> <span data-ttu-id="4d64a-128">屏幕截图显示了可视化设计图面 （左侧） 和**模型浏览器**窗口 （右侧）。</span><span class="sxs-lookup"><span data-stu-id="4d64a-128">The screenshot shows the visual design surface (to the left) and the **Model Browser** window (to the right).</span></span>

![EF 设计器 2](~/ef6/media/efdesigner2.png)

<span data-ttu-id="4d64a-130">若要撤消在 EF 设计器中完成的操作，请单击 Ctrl Z。</span><span class="sxs-lookup"><span data-stu-id="4d64a-130">To undo an operation done in the EF Designer, click Ctrl-Z.</span></span>

## <a name="working-with-diagrams"></a><span data-ttu-id="4d64a-131">使用的关系图</span><span class="sxs-lookup"><span data-stu-id="4d64a-131">Working with Diagrams</span></span>

<span data-ttu-id="4d64a-132">默认情况下 EF 设计器创建一个名为 Diagram1 的关系图。</span><span class="sxs-lookup"><span data-stu-id="4d64a-132">By default the EF Designer creates one diagram called Diagram1.</span></span> <span data-ttu-id="4d64a-133">如果您有包含大量实体和关联的关系图，您将最喜欢想要以逻辑方式拆分。</span><span class="sxs-lookup"><span data-stu-id="4d64a-133">If you have a diagram with a large number of entities and associations, you will most like want to split them up logically.</span></span> <span data-ttu-id="4d64a-134">从 Visual Studio 2012 开始，可以查看多个关系图中的概念模型。</span><span class="sxs-lookup"><span data-stu-id="4d64a-134">Starting with Visual Studio 2012, you can view your conceptual model in multiple diagrams.</span></span>   

<span data-ttu-id="4d64a-135">当您添加新关系图，它们显示在模型浏览器窗口的关系图文件夹下。</span><span class="sxs-lookup"><span data-stu-id="4d64a-135">As you add new diagrams, they appear under the Diagrams folder in the Model Browser window.</span></span> <span data-ttu-id="4d64a-136">若要重命名关系图： 模型浏览器窗口中选择关系图，一次单击名称，然后键入新名称。</span><span class="sxs-lookup"><span data-stu-id="4d64a-136">To rename a diagram: select the diagram in the Model Browser window, click once on the name, and type the new name.</span></span>  <span data-ttu-id="4d64a-137">此外可以右键单击关系图名称，然后选择**重命名**。</span><span class="sxs-lookup"><span data-stu-id="4d64a-137">You can also right-click the diagram name and select **Rename**.</span></span>

<span data-ttu-id="4d64a-138">在 Visual Studio 编辑器中的.edmx 文件名称旁边显示关系图名称。</span><span class="sxs-lookup"><span data-stu-id="4d64a-138">The diagram name is displayed next to the .edmx file name, in the Visual Studio editor.</span></span> <span data-ttu-id="4d64a-139">例如 model1.edmx\[Diagram1\]。</span><span class="sxs-lookup"><span data-stu-id="4d64a-139">For example Model1.edmx\[Diagram1\].</span></span>

![DiagramName](~/ef6/media/diagramname.png)

<span data-ttu-id="4d64a-141">关系图内容 （形状和颜色的实体和关联） 存储中。 edmx.diagram 文件。</span><span class="sxs-lookup"><span data-stu-id="4d64a-141">The diagrams content (shape and color of entities and associations) is stored in the .edmx.diagram file.</span></span> <span data-ttu-id="4d64a-142">若要查看此文件，请选择解决方案资源管理器并展开.edmx 文件。</span><span class="sxs-lookup"><span data-stu-id="4d64a-142">To view this file, select Solution Explorer and unfold the .edmx file.</span></span> 

![DiagramFiles](~/ef6/media/diagramfiles.png)

<span data-ttu-id="4d64a-144">不应编辑。 edmx.diagram 手动，文件可能被 EF 设计器覆盖此文件的内容。</span><span class="sxs-lookup"><span data-stu-id="4d64a-144">You should not edit the .edmx.diagram file manually, the content of this file maybe overwritten by the EF Designer.</span></span>
 
## <a name="splitting-entities-and-associations-into-a-new-diagram"></a><span data-ttu-id="4d64a-145">拆分实体和关联到新关系图</span><span class="sxs-lookup"><span data-stu-id="4d64a-145">Splitting Entities and Associations into a New Diagram</span></span>

<span data-ttu-id="4d64a-146">您可以选择现有的关系图 （按住 shift 键来选择多个实体） 上的实体。</span><span class="sxs-lookup"><span data-stu-id="4d64a-146">You can select entities on the existing diagram (hold Shift to select multiple entities).</span></span> <span data-ttu-id="4d64a-147">单击鼠标右键，然后选择**移动到新关系图**。</span><span class="sxs-lookup"><span data-stu-id="4d64a-147">Click the right mouse button and select **Move to new Diagram**.</span></span> <span data-ttu-id="4d64a-148">创建新关系图和所选的实体和其关联移动到关系图。</span><span class="sxs-lookup"><span data-stu-id="4d64a-148">The new diagram is created and the selected entities and their associations are moved to the diagram.</span></span>

<span data-ttu-id="4d64a-149">或者，可以右键单击模型浏览器中的关系图文件夹并选择**添加新关系图。**</span><span class="sxs-lookup"><span data-stu-id="4d64a-149">Alternatively, you can right-click the Diagrams folder in Model Browser and select **Add new Diagram.**</span></span> <span data-ttu-id="4d64a-150">你可以然后拖放实体从模型浏览器中拖动到设计图面上的实体类型文件夹下。</span><span class="sxs-lookup"><span data-stu-id="4d64a-150">You can then drag and drop entities from under the Entity Types folder in Model Browser onto the design surface.</span></span>

<span data-ttu-id="4d64a-151">此外可以剪切或将实体 （使用 Ctrl-X 或 Ctrl + C 键） 从一个关系图复制并粘贴其他 （使用 Ctrl + V 键）。</span><span class="sxs-lookup"><span data-stu-id="4d64a-151">You can also cut or copy entities (using Ctrl-X or Ctrl-C keys) from one diagram and paste (using Ctrl-V key) on the other.</span></span> <span data-ttu-id="4d64a-152">如果关系图在其中粘贴实体已包含具有相同名称的实体，将创建一个新实体，并将其添加到模型中。</span><span class="sxs-lookup"><span data-stu-id="4d64a-152">If the diagram into which you are pasting an entity already contains an entity with the same name, a new entity will be created and added to the model.</span></span>  <span data-ttu-id="4d64a-153">例如： 图 2 包含 Department 实体。</span><span class="sxs-lookup"><span data-stu-id="4d64a-153">For example: Diagram2 contains the Department entity.</span></span> <span data-ttu-id="4d64a-154">然后，将另一部门粘贴在上图 2。</span><span class="sxs-lookup"><span data-stu-id="4d64a-154">Then, you paste another Department on Diagram2.</span></span> <span data-ttu-id="4d64a-155">Department1 实体被创建并添加到概念模型。</span><span class="sxs-lookup"><span data-stu-id="4d64a-155">The Department1 entity is created and added to the conceptual model.</span></span>   

<span data-ttu-id="4d64a-156">若要在关系图中包括相关的实体，右击实体并选择**包括相关**。</span><span class="sxs-lookup"><span data-stu-id="4d64a-156">To include related entities in a diagram, rick-click the entity and select **Include Related**.</span></span> <span data-ttu-id="4d64a-157">在指定的关系图中，这将使相关的实体和关联的副本。</span><span class="sxs-lookup"><span data-stu-id="4d64a-157">This will make a copy of the related entities and associations in the specified diagram.</span></span>

## <a name="changing-the-color-of-entities"></a><span data-ttu-id="4d64a-158">更改实体的颜色</span><span class="sxs-lookup"><span data-stu-id="4d64a-158">Changing the Color of Entities</span></span>

<span data-ttu-id="4d64a-159">除了将模型拆分成多个关系图，还可以更改你的实体的颜色。</span><span class="sxs-lookup"><span data-stu-id="4d64a-159">In addition to splitting a model into multiple diagrams, you can also change colors of your entities.</span></span>

<span data-ttu-id="4d64a-160">若要更改颜色，请选择设计图面上的实体 （或多个实体）。</span><span class="sxs-lookup"><span data-stu-id="4d64a-160">To change the color, select an entity (or multiple entities) on the design surface.</span></span> <span data-ttu-id="4d64a-161">然后，单击鼠标右键并选择**属性**。</span><span class="sxs-lookup"><span data-stu-id="4d64a-161">Then, click the right mouse button and select **Properties**.</span></span> <span data-ttu-id="4d64a-162">在属性窗口中，选择**填充颜色**属性。</span><span class="sxs-lookup"><span data-stu-id="4d64a-162">In the Properties window, select the **Fill Color** property.</span></span> <span data-ttu-id="4d64a-163">指定使用有效的颜色名称 （例如，红色） 或有效的 RGB （例如，255，128，128） 的颜色。</span><span class="sxs-lookup"><span data-stu-id="4d64a-163">Specify the color using either a valid color name (for example, Red) or a valid RGB (for example, 255, 128, 128).</span></span> 

![颜色](~/ef6/media/color.png)

## <a name="summary"></a><span data-ttu-id="4d64a-165">总结</span><span class="sxs-lookup"><span data-stu-id="4d64a-165">Summary</span></span>

<span data-ttu-id="4d64a-166">本主题中介绍了如何将模型拆分为多个关系图以及如何指定不同的颜色使用实体框架设计器的实体。</span><span class="sxs-lookup"><span data-stu-id="4d64a-166">In this topic we looked at how to split a model into multiple diagrams and also how to specify a different color for an entity using the Entity Framework Designer.</span></span> 
