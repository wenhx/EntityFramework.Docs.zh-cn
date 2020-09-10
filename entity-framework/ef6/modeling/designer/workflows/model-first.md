---
title: Model First-EF6
description: 实体框架6中的 Model First
author: divega
ms.date: 10/23/2016
ms.assetid: e1b9c319-bb8a-4417-ac94-7890f257e7f6
uid: ef6/modeling/designer/workflows/model-first
ms.openlocfilehash: e995072f0f4ac23b755acc193719e5571e18f544
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620404"
---
# <a name="model-first"></a><span data-ttu-id="ca04e-103">Model First</span><span class="sxs-lookup"><span data-stu-id="ca04e-103">Model First</span></span>
<span data-ttu-id="ca04e-104">此视频和分步演练提供使用实体框架 Model First 开发的简介。</span><span class="sxs-lookup"><span data-stu-id="ca04e-104">This video and step-by-step walkthrough provide an introduction to Model First development using Entity Framework.</span></span> <span data-ttu-id="ca04e-105">Model First 允许使用 Entity Framework Designer 创建新模型，然后从该模型生成数据库架构。</span><span class="sxs-lookup"><span data-stu-id="ca04e-105">Model First allows you to create a new model using the Entity Framework Designer and then generate a database schema from the model.</span></span> <span data-ttu-id="ca04e-106">模型存储在 EDMX 文件中 ( .edmx 扩展名) 并且可以在 Entity Framework Designer 中查看和编辑。</span><span class="sxs-lookup"><span data-stu-id="ca04e-106">The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer.</span></span> <span data-ttu-id="ca04e-107">在应用程序中与之交互的类将自动从 EDMX 文件生成。</span><span class="sxs-lookup"><span data-stu-id="ca04e-107">The classes that you interact with in your application are automatically generated from the EDMX file.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="ca04e-108">观看视频</span><span class="sxs-lookup"><span data-stu-id="ca04e-108">Watch the video</span></span>
<span data-ttu-id="ca04e-109">此视频和分步演练提供使用实体框架 Model First 开发的简介。</span><span class="sxs-lookup"><span data-stu-id="ca04e-109">This video and step-by-step walkthrough provide an introduction to Model First development using Entity Framework.</span></span> <span data-ttu-id="ca04e-110">Model First 允许使用 Entity Framework Designer 创建新模型，然后从该模型生成数据库架构。</span><span class="sxs-lookup"><span data-stu-id="ca04e-110">Model First allows you to create a new model using the Entity Framework Designer and then generate a database schema from the model.</span></span> <span data-ttu-id="ca04e-111">模型存储在 EDMX 文件中 ( .edmx 扩展名) 并且可以在 Entity Framework Designer 中查看和编辑。</span><span class="sxs-lookup"><span data-stu-id="ca04e-111">The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer.</span></span> <span data-ttu-id="ca04e-112">在应用程序中与之交互的类将自动从 EDMX 文件生成。</span><span class="sxs-lookup"><span data-stu-id="ca04e-112">The classes that you interact with in your application are automatically generated from the EDMX file.</span></span>

<span data-ttu-id="ca04e-113">**主讲人**：[Rowan Miller](https://romiller.com/)</span><span class="sxs-lookup"><span data-stu-id="ca04e-113">**Presented By**: [Rowan Miller](https://romiller.com/)</span></span>

<span data-ttu-id="ca04e-114">**视频**： [wmv](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.wmv)  |  [.wmv](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-mp4video-modelfirst.m4v)  |  [wmv (ZIP) ](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.zip)</span><span class="sxs-lookup"><span data-stu-id="ca04e-114">**Video**: [WMV](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.wmv) | [MP4](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-mp4video-modelfirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="ca04e-115">先决条件</span><span class="sxs-lookup"><span data-stu-id="ca04e-115">Pre-Requisites</span></span>

<span data-ttu-id="ca04e-116">你将需要安装 Visual Studio 2010 或 Visual Studio 2012 才能完成此演练。</span><span class="sxs-lookup"><span data-stu-id="ca04e-116">You will need to have Visual Studio 2010 or Visual Studio 2012 installed to complete this walkthrough.</span></span>

<span data-ttu-id="ca04e-117">如果你使用的是 Visual Studio 2010，你还需要安装 [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) 。</span><span class="sxs-lookup"><span data-stu-id="ca04e-117">If you are using Visual Studio 2010, you will also need to have [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installed.</span></span>

## <a name="1-create-the-application"></a><span data-ttu-id="ca04e-118">1. 创建应用程序</span><span class="sxs-lookup"><span data-stu-id="ca04e-118">1. Create the Application</span></span>

<span data-ttu-id="ca04e-119">为了简单起见，我们将构建一个使用 Model First 执行数据访问的基本控制台应用程序：</span><span class="sxs-lookup"><span data-stu-id="ca04e-119">To keep things simple we’re going to build a basic console application that uses the Model First to perform data access:</span></span>

-   <span data-ttu-id="ca04e-120">打开 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ca04e-120">Open Visual Studio</span></span>
-   <span data-ttu-id="ca04e-121">**文件- &gt; 新建- &gt; 项目 .。。**</span><span class="sxs-lookup"><span data-stu-id="ca04e-121">**File -&gt; New -&gt; Project…**</span></span>
-   <span data-ttu-id="ca04e-122">从左侧菜单和**控制台应用程序**选择**Windows**</span><span class="sxs-lookup"><span data-stu-id="ca04e-122">Select **Windows** from the left menu and **Console Application**</span></span>
-   <span data-ttu-id="ca04e-123">输入 **ModelFirstSample** 作为名称</span><span class="sxs-lookup"><span data-stu-id="ca04e-123">Enter **ModelFirstSample** as the name</span></span>
-   <span data-ttu-id="ca04e-124">选择“确定”</span><span class="sxs-lookup"><span data-stu-id="ca04e-124">Select **OK**</span></span>

## <a name="2-create-model"></a><span data-ttu-id="ca04e-125">2. 创建模型</span><span class="sxs-lookup"><span data-stu-id="ca04e-125">2. Create Model</span></span>

<span data-ttu-id="ca04e-126">我们将使用在 Visual Studio 中包含的 Entity Framework Designer 来创建模型。</span><span class="sxs-lookup"><span data-stu-id="ca04e-126">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

-   <span data-ttu-id="ca04e-127">**项目- &gt; 添加新项 .。。**</span><span class="sxs-lookup"><span data-stu-id="ca04e-127">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="ca04e-128">从左侧菜单中选择 " **数据** "，然后 **ADO.NET 实体数据模型**</span><span class="sxs-lookup"><span data-stu-id="ca04e-128">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="ca04e-129">输入 **BloggingModel** 作为名称，然后单击 **"确定"**，这将启动实体数据模型向导</span><span class="sxs-lookup"><span data-stu-id="ca04e-129">Enter **BloggingModel** as the name and click **OK**, this launches the Entity Data Model Wizard</span></span>
-   <span data-ttu-id="ca04e-130">选择 "**空模型**"，然后单击 "**完成**"</span><span class="sxs-lookup"><span data-stu-id="ca04e-130">Select **Empty Model** and click **Finish**</span></span>

    ![创建空模型](~/ef6/media/createemptymodel.png)

<span data-ttu-id="ca04e-132">将使用空白模型打开 Entity Framework Designer。</span><span class="sxs-lookup"><span data-stu-id="ca04e-132">The Entity Framework Designer is opened with a blank model.</span></span> <span data-ttu-id="ca04e-133">现在，我们可以开始向模型添加实体、属性和关联。</span><span class="sxs-lookup"><span data-stu-id="ca04e-133">Now we can start adding entities, properties and associations to the model.</span></span>

-   <span data-ttu-id="ca04e-134">右键单击设计图面，然后选择 "**属性**"</span><span class="sxs-lookup"><span data-stu-id="ca04e-134">Right-click on the design surface and select **Properties**</span></span>
-   <span data-ttu-id="ca04e-135">在属性窗口将**实体容器名称**更改为 **"bloggingcontext"** 
     *，这是将为你生成的派生上下文的名称，上下文表示与数据库的会话，从而使我们能够查询和保存数据*</span><span class="sxs-lookup"><span data-stu-id="ca04e-135">In the Properties window change the **Entity Container Name** to **BloggingContext**
*This is the name of the derived context that will be generated for you, the context represents a session with the database, allowing us to query and save data*</span></span>
-   <span data-ttu-id="ca04e-136">右键单击设计图面，然后选择 "**添加新 &gt; 实体 ...** "</span><span class="sxs-lookup"><span data-stu-id="ca04e-136">Right-click on the design surface and select **Add New -&gt; Entity…**</span></span>
-   <span data-ttu-id="ca04e-137">以 "实体名称" 和 " **BlogId** " 作为键名称输入**博客**，然后单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="ca04e-137">Enter **Blog** as the entity name and **BlogId** as the key name and click **OK**</span></span>

    ![添加博客实体](~/ef6/media/addblogentity.png)

-   <span data-ttu-id="ca04e-139">右键单击设计图面上的新实体，然后选择 " **添加新 &gt; 标量属性**"，输入 **name** 作为属性的名称。</span><span class="sxs-lookup"><span data-stu-id="ca04e-139">Right-click on the new entity on the design surface and select **Add New -&gt; Scalar Property**, enter **Name** as the name of the property.</span></span>
-   <span data-ttu-id="ca04e-140">重复此过程以添加 **Url** 属性。</span><span class="sxs-lookup"><span data-stu-id="ca04e-140">Repeat this process to add a **Url** property.</span></span>
-   <span data-ttu-id="ca04e-141">右键单击设计图面上的 " **Url** " 属性，然后选择 "**属性**"，在 "属性窗口将**可为 null**的设置更改为**True**， 
     *这允许我们将博客保存到数据库，而无需向其分配 Url*</span><span class="sxs-lookup"><span data-stu-id="ca04e-141">Right-click on the **Url** property on the design surface and select **Properties**, in the Properties window change the **Nullable** setting to **True**
*This allows us to save a Blog to the database without assigning it a Url*</span></span>
-   <span data-ttu-id="ca04e-142">使用刚才了解到的技术，添加具有**PostId**键属性的**Post**实体</span><span class="sxs-lookup"><span data-stu-id="ca04e-142">Using the techniques you just learnt, add a **Post** entity with a **PostId** key property</span></span>
-   <span data-ttu-id="ca04e-143">向**Post**实体添加**Title**和**Content**标量属性</span><span class="sxs-lookup"><span data-stu-id="ca04e-143">Add **Title** and **Content** scalar properties to the **Post** entity</span></span>

<span data-ttu-id="ca04e-144">现在我们有了几个实体，接下来可以在它们之间添加关联 (或关系) 。</span><span class="sxs-lookup"><span data-stu-id="ca04e-144">Now that we have a couple of entities, it’s time to add an association (or relationship) between them.</span></span>

-   <span data-ttu-id="ca04e-145">右键单击设计图面，然后选择 "**添加新 &gt; 关联 ...** "</span><span class="sxs-lookup"><span data-stu-id="ca04e-145">Right-click on the design surface and select **Add New -&gt; Association…**</span></span>
-   <span data-ttu-id="ca04e-146">使关系点的一端成为使用**1**的重**数的\*\*\*\*博客**，另一个终结点**的重数，** 
     *这意味着，博客包含多篇文章，张贴内容属于一个博客*</span><span class="sxs-lookup"><span data-stu-id="ca04e-146">Make one end of the relationship point to **Blog** with a multiplicity of **One** and the other end point to **Post** with a multiplicity of **Many**
*This means that a Blog has many Posts and a Post belongs to one Blog*</span></span>
-   <span data-ttu-id="ca04e-147">确保选中 "**将外键属性添加到 ' Post ' 实体**" 框，然后单击 **"确定**"</span><span class="sxs-lookup"><span data-stu-id="ca04e-147">Ensure the **Add foreign key properties to 'Post' Entity** box is checked and click **OK**</span></span>

    ![添加 Association MF](~/ef6/media/addassociationmf.png)

<span data-ttu-id="ca04e-149">现在，我们有了一个简单的模型，我们可以从它生成数据库并使用它来读取和写入数据。</span><span class="sxs-lookup"><span data-stu-id="ca04e-149">We now have a simple model that we can generate a database from and use to read and write data.</span></span>

![模型初始](~/ef6/media/modelinitial.png)

### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="ca04e-151">Visual Studio 2010 中的其他步骤</span><span class="sxs-lookup"><span data-stu-id="ca04e-151">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="ca04e-152">如果使用的是 Visual Studio 2010，升级到最新版本的实体框架需要遵循一些额外步骤。</span><span class="sxs-lookup"><span data-stu-id="ca04e-152">If you are working in Visual Studio 2010 there are some additional steps you need to follow to upgrade to the latest version of Entity Framework.</span></span> <span data-ttu-id="ca04e-153">升级很重要，因为它使你能够访问改进的 API 图面，更易于使用，并提供最新的 bug 修复。</span><span class="sxs-lookup"><span data-stu-id="ca04e-153">Upgrading is important because it gives you access to an improved API surface, that is much easier to use, as well as the latest bug fixes.</span></span>

<span data-ttu-id="ca04e-154">首先，我们需要从 NuGet 获取最新版本的实体框架。</span><span class="sxs-lookup"><span data-stu-id="ca04e-154">First up, we need to get the latest version of Entity Framework from NuGet.</span></span>

-   <span data-ttu-id="ca04e-155">**项目– &gt;管理 NuGet 包 ...** 
    *如果你没有 "\*\*管理 NuGet 包 ...*\* " 选项，则应安装[最新版本的 nuget](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) \*</span><span class="sxs-lookup"><span data-stu-id="ca04e-155">**Project –&gt; Manage NuGet Packages…**
*If you don’t have the **Manage NuGet Packages…** option you should install the [latest version of NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)*</span></span>
-   <span data-ttu-id="ca04e-156">选择 " **联机** " 选项卡</span><span class="sxs-lookup"><span data-stu-id="ca04e-156">Select the **Online** tab</span></span>
-   <span data-ttu-id="ca04e-157">选择 **EntityFramework** 包</span><span class="sxs-lookup"><span data-stu-id="ca04e-157">Select the **EntityFramework** package</span></span>
-   <span data-ttu-id="ca04e-158">单击“安装” </span><span class="sxs-lookup"><span data-stu-id="ca04e-158">Click **Install**</span></span>

<span data-ttu-id="ca04e-159">接下来，我们需要交换模型，以生成使用 DbContext API 的代码，这些代码是在实体框架的更高版本中引入的。</span><span class="sxs-lookup"><span data-stu-id="ca04e-159">Next, we need to swap our model to generate code that makes use of the DbContext API, which was introduced in later versions of Entity Framework.</span></span>

-   <span data-ttu-id="ca04e-160">在 EF 设计器中右键单击模型的空位置，然后选择 "**添加代码生成项 ...** "</span><span class="sxs-lookup"><span data-stu-id="ca04e-160">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
-   <span data-ttu-id="ca04e-161">从左侧菜单中选择 " **联机模板** "，然后搜索 **DbContext**</span><span class="sxs-lookup"><span data-stu-id="ca04e-161">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
-   <span data-ttu-id="ca04e-162">选择**适用于 C \# 的 EF DbContext 生成器**，输入**BloggingModel**作为名称，然后单击 "**添加**"</span><span class="sxs-lookup"><span data-stu-id="ca04e-162">Select the EF **5.x DbContext Generator for C\#**, enter **BloggingModel** as the name and click **Add**</span></span>

    ![DbContext 模板](~/ef6/media/dbcontexttemplate.png)

## <a name="3-generating-the-database"></a><span data-ttu-id="ca04e-164">3. 生成数据库</span><span class="sxs-lookup"><span data-stu-id="ca04e-164">3. Generating the Database</span></span>

<span data-ttu-id="ca04e-165">考虑到我们的模型，实体框架可以计算一个数据库架构，该架构将允许我们使用模型存储和检索数据。</span><span class="sxs-lookup"><span data-stu-id="ca04e-165">Given our model, Entity Framework can calculate a database schema that will allow us to store and retrieve data using the model.</span></span>

<span data-ttu-id="ca04e-166">随 Visual Studio 一起安装的数据库服务器因安装的 Visual Studio 版本而异：</span><span class="sxs-lookup"><span data-stu-id="ca04e-166">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="ca04e-167">如果使用的是 Visual Studio 2010，则将创建 SQL Express 数据库。</span><span class="sxs-lookup"><span data-stu-id="ca04e-167">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
-   <span data-ttu-id="ca04e-168">如果使用的是 Visual Studio 2012，则将创建一个 [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) 数据库。</span><span class="sxs-lookup"><span data-stu-id="ca04e-168">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) database.</span></span>

<span data-ttu-id="ca04e-169">接下来，生成数据库。</span><span class="sxs-lookup"><span data-stu-id="ca04e-169">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="ca04e-170">右键单击设计图面，然后选择 "**从模型生成数据库 ...** "</span><span class="sxs-lookup"><span data-stu-id="ca04e-170">Right-click on the design surface and select **Generate Database from Model…**</span></span>
-   <span data-ttu-id="ca04e-171">单击 "**新建连接 ...** "</span><span class="sxs-lookup"><span data-stu-id="ca04e-171">Click **New Connection…**</span></span> <span data-ttu-id="ca04e-172">并指定 LocalDB 或 SQL Express，具体取决于所使用的 Visual Studio 的版本，请输入 **ModelFirst** 作为数据库名称。</span><span class="sxs-lookup"><span data-stu-id="ca04e-172">and specify either LocalDB or SQL Express, depending on which version of Visual Studio you are using, enter **ModelFirst.Blogging** as the database name.</span></span>

    ![LocalDB 连接 MF](~/ef6/media/localdbconnectionmf.png)

    ![Sql Express 连接 MF](~/ef6/media/sqlexpressconnectionmf.png)

-   <span data-ttu-id="ca04e-175">选择 **"确定"** ，系统会询问您是否要创建新数据库，请选择 **"是"**</span><span class="sxs-lookup"><span data-stu-id="ca04e-175">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>
-   <span data-ttu-id="ca04e-176">选择 " **下一步** "，Entity Framework Designer 将计算用于创建数据库架构的脚本</span><span class="sxs-lookup"><span data-stu-id="ca04e-176">Select **Next** and the Entity Framework Designer will calculate a script to create the database schema</span></span>
-   <span data-ttu-id="ca04e-177">显示脚本后，单击 " **完成** "，脚本将添加到项目中并打开</span><span class="sxs-lookup"><span data-stu-id="ca04e-177">Once the script is displayed, click **Finish** and the script will be added to your project and opened</span></span>
-   <span data-ttu-id="ca04e-178">右键单击该脚本，然后选择 " **执行**"，系统将提示您指定要连接到的数据库，指定 LocalDB 或 SQL Server Express，具体取决于您使用的 Visual Studio 的版本</span><span class="sxs-lookup"><span data-stu-id="ca04e-178">Right-click on the script and select **Execute**, you will be prompted to specify the database to connect to, specify LocalDB or SQL Server Express, depending on which version of Visual Studio you are using</span></span>

## <a name="4-reading--writing-data"></a><span data-ttu-id="ca04e-179">4. 读取 & 写入数据</span><span class="sxs-lookup"><span data-stu-id="ca04e-179">4. Reading & Writing Data</span></span>

<span data-ttu-id="ca04e-180">现在，我们有了一个模型，可以使用它来访问某些数据了。</span><span class="sxs-lookup"><span data-stu-id="ca04e-180">Now that we have a model it’s time to use it to access some data.</span></span> <span data-ttu-id="ca04e-181">要用于访问数据的类将根据 EDMX 文件自动生成。</span><span class="sxs-lookup"><span data-stu-id="ca04e-181">The classes we are going to use to access data are being automatically generated for you based on the EDMX file.</span></span>

<span data-ttu-id="ca04e-182">*此屏幕快照来自 Visual Studio 2012，如果你使用的是 Visual Studio 2010，则 BloggingModel.tt 和 BloggingModel.Context.tt 文件将直接位于你的项目下，而不是在 EDMX 文件下嵌套。*</span><span class="sxs-lookup"><span data-stu-id="ca04e-182">*This screen shot is from Visual Studio 2012, if you are using Visual Studio 2010 the BloggingModel.tt and BloggingModel.Context.tt files will be directly under your project rather than nested under the EDMX file.*</span></span>

![生成的类](~/ef6/media/generatedclasses.png)

<span data-ttu-id="ca04e-184">在 Program.cs 中实现 Main 方法，如下所示。</span><span class="sxs-lookup"><span data-stu-id="ca04e-184">Implement the Main method in Program.cs as shown below.</span></span> <span data-ttu-id="ca04e-185">此代码创建一个新的上下文实例，然后使用它来插入新的博客。</span><span class="sxs-lookup"><span data-stu-id="ca04e-185">This code creates a new instance of our context and then uses it to insert a new Blog.</span></span> <span data-ttu-id="ca04e-186">然后，它使用 LINQ 查询从按标题字母顺序排序的数据库中检索所有博客。</span><span class="sxs-lookup"><span data-stu-id="ca04e-186">Then it uses a LINQ query to retrieve all Blogs from the database ordered alphabetically by Title.</span></span>

``` csharp
class Program
{
    static void Main(string[] args)
    {
        using (var db = new BloggingContext())
        {
            // Create and save a new Blog
            Console.Write("Enter a name for a new Blog: ");
            var name = Console.ReadLine();

            var blog = new Blog { Name = name };
            db.Blogs.Add(blog);
            db.SaveChanges();

            // Display all Blogs from the database
            var query = from b in db.Blogs
                        orderby b.Name
                        select b;

            Console.WriteLine("All blogs in the database:");
            foreach (var item in query)
            {
                Console.WriteLine(item.Name);
            }

            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
        }
    }
}
```

<span data-ttu-id="ca04e-187">你现在可以运行该应用程序并对其进行测试。</span><span class="sxs-lookup"><span data-stu-id="ca04e-187">You can now run the application and test it out.</span></span>

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```

## <a name="5-dealing-with-model-changes"></a><span data-ttu-id="ca04e-188">5. 处理模型更改</span><span class="sxs-lookup"><span data-stu-id="ca04e-188">5. Dealing with Model Changes</span></span>

<span data-ttu-id="ca04e-189">现在，可以对模型进行一些更改，当我们进行这些更改时，我们还需要更新数据库架构。</span><span class="sxs-lookup"><span data-stu-id="ca04e-189">Now it’s time to make some changes to our model, when we make these changes we also need to update the database schema.</span></span>

<span data-ttu-id="ca04e-190">首先向模型添加新的用户实体。</span><span class="sxs-lookup"><span data-stu-id="ca04e-190">We’ll start by adding a new User entity to our model.</span></span>

-   <span data-ttu-id="ca04e-191">添加新的 **用户** 实体名称，其中 **用户名** 为密钥名称， **字符串** 用作密钥的属性类型</span><span class="sxs-lookup"><span data-stu-id="ca04e-191">Add a new **User** entity name with **Username** as the key name and **String** as the property type for the key</span></span>

    ![添加用户实体](~/ef6/media/adduserentity.png)

-   <span data-ttu-id="ca04e-193">右键单击设计图面上的 "**用户名**" 属性，然后选择 "**属性**"，在 "属性窗口将" **MaxLength** "设置更改为" **50**"， 
     *这会将用户名中存储的数据限制为50个字符*</span><span class="sxs-lookup"><span data-stu-id="ca04e-193">Right-click on the **Username** property on the design surface and select **Properties**, In the Properties window change the **MaxLength** setting to **50**
*This restricts the data that can be stored in username to 50 characters*</span></span>
-   <span data-ttu-id="ca04e-194">向**用户**实体添加**DisplayName**标量属性</span><span class="sxs-lookup"><span data-stu-id="ca04e-194">Add a **DisplayName** scalar property to the **User** entity</span></span>

<span data-ttu-id="ca04e-195">现在，我们有了一个更新的模型，我们可以更新数据库以适应新的用户实体类型。</span><span class="sxs-lookup"><span data-stu-id="ca04e-195">We now have an updated model and we are ready to update the database to accommodate our new User entity type.</span></span>

-   <span data-ttu-id="ca04e-196">右键单击设计图面，然后选择 " **从模型生成数据库 ...**"，实体框架将计算一个脚本以根据更新后的模型重新创建架构。</span><span class="sxs-lookup"><span data-stu-id="ca04e-196">Right-click on the design surface and select **Generate Database from Model…**, Entity Framework will calculate a script to recreate a schema based on the updated model.</span></span>
-   <span data-ttu-id="ca04e-197">单击“完成”</span><span class="sxs-lookup"><span data-stu-id="ca04e-197">Click **Finish**</span></span>
-   <span data-ttu-id="ca04e-198">你可能会收到有关覆盖模型的现有 DDL 脚本以及映射和存储部分的警告，请单击 **"是"** 以显示这两个警告</span><span class="sxs-lookup"><span data-stu-id="ca04e-198">You may receive warnings about overwriting the existing DDL script and the mapping and storage parts of the model, click **Yes** for both these warnings</span></span>
-   <span data-ttu-id="ca04e-199">将为您打开用于创建数据库的更新的 SQL 脚本</span><span class="sxs-lookup"><span data-stu-id="ca04e-199">The updated SQL script to create the database is opened for you</span></span>  
    <span data-ttu-id="ca04e-200">*生成的脚本将删除所有现有的表，然后从头开始重新创建该架构。这可能适用于本地开发，但并不适合将更改推送到已部署的数据库。如果需要将更改发布到已部署的数据库，则需要编辑该脚本或使用架构比较工具来计算迁移脚本。*</span><span class="sxs-lookup"><span data-stu-id="ca04e-200">*The script that is generated will drop all existing tables and then recreate the schema from scratch. This may work for local development but is not a viable for pushing changes to a database that has already been deployed. If you need to publish changes to a database that has already been deployed, you will need to edit the script or use a schema compare tool to calculate a migration script.*</span></span>
-   <span data-ttu-id="ca04e-201">右键单击该脚本，然后选择 " **执行**"，系统将提示您指定要连接到的数据库，指定 LocalDB 或 SQL Server Express，具体取决于您使用的 Visual Studio 的版本</span><span class="sxs-lookup"><span data-stu-id="ca04e-201">Right-click on the script and select **Execute**, you will be prompted to specify the database to connect to, specify LocalDB or SQL Server Express, depending on which version of Visual Studio you are using</span></span>

## <a name="summary"></a><span data-ttu-id="ca04e-202">总结</span><span class="sxs-lookup"><span data-stu-id="ca04e-202">Summary</span></span>

<span data-ttu-id="ca04e-203">在本演练中，我们介绍 Model First 开发，这允许我们在 EF 设计器中创建模型，然后从该模型生成数据库。</span><span class="sxs-lookup"><span data-stu-id="ca04e-203">In this walkthrough we looked at Model First development, which allowed us to create a model in the EF Designer and then generate a database from that model.</span></span> <span data-ttu-id="ca04e-204">然后，使用该模型从数据库中读取和写入一些数据。</span><span class="sxs-lookup"><span data-stu-id="ca04e-204">We then used the model to read and write some data from the database.</span></span> <span data-ttu-id="ca04e-205">最后，我们更新了模型，然后重新创建了数据库架构以匹配模型。</span><span class="sxs-lookup"><span data-stu-id="ca04e-205">Finally, we updated the model and then recreated the database schema to match the model.</span></span>
