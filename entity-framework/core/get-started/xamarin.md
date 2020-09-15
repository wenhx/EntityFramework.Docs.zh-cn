---
title: EF Core 和 Xamarin 入门 - EF Core
description: 将 Entity Framework Core 和 Xamarin 结合使用来创建移动应用的入门教程
author: codemillmatt
ms.date: 07/07/2020
ms.author: masoucou
uid: core/get-started/xamarin
ms.openlocfilehash: b4a7e2260337d74329d309e9db32fe97a2131d73
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619297"
---
# <a name="getting-started-with-ef-core-and-xamarin"></a><span data-ttu-id="4115a-103">EF Core 和 Xamarin 入门</span><span class="sxs-lookup"><span data-stu-id="4115a-103">Getting Started with EF Core and Xamarin</span></span>

<span data-ttu-id="4115a-104">在本教程中，你将创建一个 [Xamarin.Forms](/xamarin/get-started/what-is-xamarin-forms) 应用，该应用使用 Entity Framework Core 对 SQLite 数据库执行数据访问。</span><span class="sxs-lookup"><span data-stu-id="4115a-104">In this tutorial, you create a [Xamarin.Forms](/xamarin/get-started/what-is-xamarin-forms) application that performs data access against a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="4115a-105">可使用 Windows 版 Visual Studio 或 Visual Studio for Mac 来执行本教程中的操作。</span><span class="sxs-lookup"><span data-stu-id="4115a-105">You can follow the tutorial by using Visual Studio on Windows or Visual Studio for Mac.</span></span>

> [!TIP]
> <span data-ttu-id="4115a-106">可在 [GitHub 示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin)中查看此文章的示例。</span><span class="sxs-lookup"><span data-stu-id="4115a-106">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4115a-107">先决条件</span><span class="sxs-lookup"><span data-stu-id="4115a-107">Prerequisites</span></span>

<span data-ttu-id="4115a-108">安装以下工具之一：</span><span class="sxs-lookup"><span data-stu-id="4115a-108">Install one of the below:</span></span>

* <span data-ttu-id="4115a-109">对于此工作负载，建议采用 [Visual Studio 2019 版本 16.3 或更高版本](https://www.visualstudio.com/downloads/)：</span><span class="sxs-lookup"><span data-stu-id="4115a-109">[Visual Studio 2019 version 16.3 or later](https://www.visualstudio.com/downloads/) with this workload:</span></span>
  * <span data-ttu-id="4115a-110">使用 .NET 的移动开发</span><span class="sxs-lookup"><span data-stu-id="4115a-110">**Mobile Development with .NET**</span></span>
* [<span data-ttu-id="4115a-111">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4115a-111">Visual Studio for Mac</span></span>](https://visualstudio.microsoft.com/vs/mac/)

<span data-ttu-id="4115a-112">该[文档提供每个平台的详细分步安装说明](/xamarin/get-started/installation)。</span><span class="sxs-lookup"><span data-stu-id="4115a-112">This [documentation provides detailed step-by-step installation instructions](/xamarin/get-started/installation) for each platform.</span></span>

## <a name="download-and-run-the-sample-project"></a><span data-ttu-id="4115a-113">下载并运行示例项目</span><span class="sxs-lookup"><span data-stu-id="4115a-113">Download and run the sample project</span></span>

<span data-ttu-id="4115a-114">若要运行和浏览此示例应用程序，请下载 GitHub 上的代码。</span><span class="sxs-lookup"><span data-stu-id="4115a-114">To run and explore this sample application, download the code on GitHub.</span></span>

<span data-ttu-id="4115a-115">下载后，在 Visual Studio 或 Visual Studio for Mac 中打开解决方案文件 `EFGettingStarted.sln`，并在所选平台上运行应用程序。</span><span class="sxs-lookup"><span data-stu-id="4115a-115">Once downloaded, open the solution file `EFGettingStarted.sln` in Visual Studio or Visual Studio for Mac and run the application on the platform of your choice.</span></span>

<span data-ttu-id="4115a-116">应用首次启动时，将填充本地 SQLite 数据库（其中包含两个表示博客的条目）。</span><span class="sxs-lookup"><span data-stu-id="4115a-116">When the app first starts, it will populate the local SQLite database with two entries representing blogs.</span></span>

![包含所有博客的列表页的屏幕截图](_static/xamarin-tutorial-1.png)

<span data-ttu-id="4115a-118">单击工具栏中的“添加”按钮。</span><span class="sxs-lookup"><span data-stu-id="4115a-118">Click the **Add** button in the toolbar.</span></span>

<span data-ttu-id="4115a-119">随即将显示一个新的页面，你可在该页面输入新博客的相关信息。</span><span class="sxs-lookup"><span data-stu-id="4115a-119">A new page will appear that allows you to enter information about a new blog.</span></span>

![新博客编辑页的屏幕截图](_static/xamarin-tutorial-2.png)

<span data-ttu-id="4115a-121">填写所有信息，然后单击工具栏中的“保存”。</span><span class="sxs-lookup"><span data-stu-id="4115a-121">Fill out all the info and click **Save** from the toolbar.</span></span> <span data-ttu-id="4115a-122">新博客将保存到应用的 SQLite 数据库中，并显示在列表中。</span><span class="sxs-lookup"><span data-stu-id="4115a-122">The new blog will save to the app's SQLite database and will show in the list.</span></span>

<span data-ttu-id="4115a-123">你可单击列表中的一个博客条目，并查看该博客的任何帖子。</span><span class="sxs-lookup"><span data-stu-id="4115a-123">You can click on one of the blog entries in the list and see any posts for that blog.</span></span>

![博客文章列表页的屏幕截图](_static/xamarin-tutorial-3.png)

<span data-ttu-id="4115a-125">在工具栏中，单击“添加”。</span><span class="sxs-lookup"><span data-stu-id="4115a-125">Click **Add** in the toolbar.</span></span>

<span data-ttu-id="4115a-126">此时将显示一个页面，你可在该页面中填写新博客文章的相关信息。</span><span class="sxs-lookup"><span data-stu-id="4115a-126">A page then appears that allows you to fill out information about a new blog post.</span></span>

![“添加新的文章”页面的屏幕截图](_static/xamarin-tutorial-4.png)

<span data-ttu-id="4115a-128">填写所有信息，然后单击工具栏中的“保存”。</span><span class="sxs-lookup"><span data-stu-id="4115a-128">Fill out all the information and click **Save** in the toolbar.</span></span>

<span data-ttu-id="4115a-129">新文章将与上一步中单击的博客文章关联，它将保存到应用的 SQLite 数据库并在列表中显示。</span><span class="sxs-lookup"><span data-stu-id="4115a-129">The new post will be associated to the blog post you clicked on in a previous step and will save to the app's SQLite database and show in the list.</span></span>

<span data-ttu-id="4115a-130">返回到博客列表页。</span><span class="sxs-lookup"><span data-stu-id="4115a-130">Go back to the blog list page.</span></span> <span data-ttu-id="4115a-131">然后单击工具栏中的“全部删除”。</span><span class="sxs-lookup"><span data-stu-id="4115a-131">And click **Delete All** in the toolbar.</span></span> <span data-ttu-id="4115a-132">所有博客及其对应的文章随后都将从应用的 SQLite 数据库中删除。</span><span class="sxs-lookup"><span data-stu-id="4115a-132">All blogs and their corresponding posts will then be deleted from the app's SQLite database.</span></span>

![博客被全部删除的应用的屏幕截图](_static/xamarin-tutorial-5.png)

## <a name="explore-the-code"></a><span data-ttu-id="4115a-134">浏览代码</span><span class="sxs-lookup"><span data-stu-id="4115a-134">Explore the code</span></span>

<span data-ttu-id="4115a-135">以下各部分将展示示例项目中的代码，该项目会搭配 EF Core 使用 Xamarin.Forms 从 SQLite 数据库读取、创建、更新和删除数据。</span><span class="sxs-lookup"><span data-stu-id="4115a-135">The following sections will walk you through the code in the sample project that reads, creates, updates, and deletes data from a SQLite database using EF Core with Xamarin.Forms.</span></span>

<span data-ttu-id="4115a-136">本文假设你熟悉 Xamarin. Forms 的[显示数据](/xamarin/xamarin-forms/app-fundamentals/data-binding/)和[在页面之间导航](/xamarin/xamarin-forms/app-fundamentals/navigation/)主题。</span><span class="sxs-lookup"><span data-stu-id="4115a-136">It is assumed that you are familiar with the Xamarin.Forms topics of [displaying data](/xamarin/xamarin-forms/app-fundamentals/data-binding/) and [navigating between pages](/xamarin/xamarin-forms/app-fundamentals/navigation/).</span></span>

## <a name="entity-framework-core-nuget-packages"></a><span data-ttu-id="4115a-137">Entity Framework Core NuGet 包</span><span class="sxs-lookup"><span data-stu-id="4115a-137">Entity Framework Core NuGet packages</span></span>

<span data-ttu-id="4115a-138">若要使用 EF Core 创建 Xamarin. Forms 应用，请将要以其为目标的 EF Core 数据库提供程序的包安装到 Xamarin.Forms 解决方案中的所有项目。</span><span class="sxs-lookup"><span data-stu-id="4115a-138">To create Xamarin.Forms apps with EF Core, you install the package for the EF Core database provider(s) you want to target into all of the projects in the Xamarin.Forms solution.</span></span> <span data-ttu-id="4115a-139">本教程使用 SQLite 提供程序。</span><span class="sxs-lookup"><span data-stu-id="4115a-139">This tutorial uses the SQLite provider.</span></span>

<span data-ttu-id="4115a-140">Xamarin.Forms 解决方案的每个项目中都需要以下 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="4115a-140">The following NuGet package is needed in each of the projects in the Xamarin.Forms solution.</span></span>

* `Microsoft.EntityFrameworkCore.Sqlite`

## <a name="model-classes"></a><span data-ttu-id="4115a-141">模型类</span><span class="sxs-lookup"><span data-stu-id="4115a-141">Model classes</span></span>

<span data-ttu-id="4115a-142">通过 EF Core 访问的 SQLite 数据库中的每个表都在某个类中进行建模。</span><span class="sxs-lookup"><span data-stu-id="4115a-142">Each table in the SQLite database accessed through EF Core is modeled in a class.</span></span> <span data-ttu-id="4115a-143">在本例中使用了两个类 - `Blog` 和 `Post`，它们位于 `Models` 文件夹。</span><span class="sxs-lookup"><span data-stu-id="4115a-143">In this sample, two classes are used: `Blog` and `Post` which can be found in the `Models` folder.</span></span>

<span data-ttu-id="4115a-144">模型类仅由属性（在数据库中对列建模）组成。</span><span class="sxs-lookup"><span data-stu-id="4115a-144">The model classes are composed only of properties, which model columns in the database.</span></span>

* <span data-ttu-id="4115a-145">**Blog.cs**</span><span class="sxs-lookup"><span data-stu-id="4115a-145">**Blog.cs**</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Blog.cs)]

* <span data-ttu-id="4115a-146">`Posts` 属性会定义 `Blog` 与 `Post` 之间的父子关系。</span><span class="sxs-lookup"><span data-stu-id="4115a-146">The `Posts` property defines a parent-child relationship between `Blog` and `Post`.</span></span>

* <span data-ttu-id="4115a-147">**Post.cs**</span><span class="sxs-lookup"><span data-stu-id="4115a-147">**Post.cs**</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Post.cs)]

* <span data-ttu-id="4115a-148">`BlogId` 和 `Blog` 属性与 `Post` 的实例的父级 `Blog` 对象相关联。</span><span class="sxs-lookup"><span data-stu-id="4115a-148">The `BlogId` and `Blog` properties relate back to the parent `Blog` object for the instance of the `Post`.</span></span>

## <a name="data-context"></a><span data-ttu-id="4115a-149">数据上下文</span><span class="sxs-lookup"><span data-stu-id="4115a-149">Data context</span></span>

<span data-ttu-id="4115a-150">`BloggingContext` 类位于 `Services` 文件夹中，并继承自 EF Core `DbContext` 类。</span><span class="sxs-lookup"><span data-stu-id="4115a-150">The `BloggingContext` class is located in the `Services` folder and inherits from the EF Core `DbContext` class.</span></span> <span data-ttu-id="4115a-151">`DbContext` 用于将数据库查询和更改组合到一起。</span><span class="sxs-lookup"><span data-stu-id="4115a-151">A `DbContext` is used to group together database queries and changes.</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Services/BloggingContext.cs)]

* <span data-ttu-id="4115a-152">`DbSet` 类中的两个属性都用于对表示博客和文章的基础表进行操作。</span><span class="sxs-lookup"><span data-stu-id="4115a-152">Both properties in this class of type `DbSet` are used to operate on the underlying tables representing Blogs and Posts.</span></span>
* <span data-ttu-id="4115a-153">构造函数中需要 `SQLitePCL.Batteries_V2.Init()` 才能在 iOS 上启动 SQLite。</span><span class="sxs-lookup"><span data-stu-id="4115a-153">The `SQLitePCL.Batteries_V2.Init()` is needed in the constructor to initiate SQLite on iOS.</span></span>
* <span data-ttu-id="4115a-154">`OnConfiguring` 函数可在物理设备上设置 SQLite 数据库的位置。</span><span class="sxs-lookup"><span data-stu-id="4115a-154">The `OnConfiguring` function sets up the location of the SQLite database on the physical device.</span></span>

## <a name="create-read-update--delete"></a><span data-ttu-id="4115a-155">创建、读取、更新和删除</span><span class="sxs-lookup"><span data-stu-id="4115a-155">Create, read, update & delete</span></span>

<span data-ttu-id="4115a-156">下面是应用中的一些实例，其中 EF Core 用于访问 SQLite。</span><span class="sxs-lookup"><span data-stu-id="4115a-156">The following are some instances in the app where EF Core is used to access SQLite.</span></span>

### <a name="read"></a><span data-ttu-id="4115a-157">读取</span><span class="sxs-lookup"><span data-stu-id="4115a-157">Read</span></span>

* <span data-ttu-id="4115a-158">返回所有记录。</span><span class="sxs-lookup"><span data-stu-id="4115a-158">Return all records.</span></span>
  * <span data-ttu-id="4115a-159">`BlogsPage.xaml.cs` 的 `OnAppearing` 函数可返回所有 `Blog` 记录，并将它们存储在 `List` 变量中。</span><span class="sxs-lookup"><span data-stu-id="4115a-159">The `OnAppearing` function of `BlogsPage.xaml.cs` returns all `Blog` records and stores them into a `List` variable.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    var theBlogs = blogContext.Blogs.ToList();
}
```

* <span data-ttu-id="4115a-160">返回特定记录。</span><span class="sxs-lookup"><span data-stu-id="4115a-160">Return specific records.</span></span>
  * <span data-ttu-id="4115a-161">`PostsPage.xaml.cs` 的 `OnAppearing` 函数可返回包含特定 `BlogId` 的 `Post` 记录。</span><span class="sxs-lookup"><span data-stu-id="4115a-161">The `OnAppearing` function of `PostsPage.xaml.cs` returns `Post` records that contain a specific `BlogId`.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    var postList = blogContext.Posts
        .Where(p => p.BlogId == BlogId)
        .ToList();
}
```

### <a name="create"></a><span data-ttu-id="4115a-162">创建</span><span class="sxs-lookup"><span data-stu-id="4115a-162">Create</span></span>

* <span data-ttu-id="4115a-163">插入新记录。</span><span class="sxs-lookup"><span data-stu-id="4115a-163">Insert a new record.</span></span>
  * <span data-ttu-id="4115a-164">`AddBlogPage.xaml.cs` 的 `Save_Clicked` 函数可将新的 `Blog` 对象插入到 SQLite 数据库中。</span><span class="sxs-lookup"><span data-stu-id="4115a-164">The `Save_Clicked` function of `AddBlogPage.xaml.cs` inserts a new `Blog` object into the SQLite database.</span></span>

```csharp
var blog = new Blog { Url = blogUrl.Text };

using (var blogContext = new BloggingContext())
{
    blogContext.Add(blog);

    await blogContext.SaveChangesAsync();
}
```

### <a name="update"></a><span data-ttu-id="4115a-165">更新</span><span class="sxs-lookup"><span data-stu-id="4115a-165">Update</span></span>

* <span data-ttu-id="4115a-166">更新现有记录。</span><span class="sxs-lookup"><span data-stu-id="4115a-166">Update an existing record.</span></span>
  * <span data-ttu-id="4115a-167">`AddPostPage.xaml.cs` 的 `Save_Clicked` 函数使用新的 `Post` 更新现有的 `Blog` 对象。</span><span class="sxs-lookup"><span data-stu-id="4115a-167">The `Save_Clicked` function of `AddPostPage.xaml.cs` updates an existing `Blog` object with a new `Post`.</span></span>
  
```csharp
var newPost = new Post
{
    BlogId = BlogId,
    Content = postCell.Text,
    Title = titleCell.Text
};

using (var blogContext = new BloggingContext())
{
    var blog = await blogContext
        .Blogs
        .FirstAsync(b => b.BlogId == BlogId);

    blog.Posts.Add(newPost);

    await blogContext.SaveChangesAsync();
}
```

### <a name="delete"></a><span data-ttu-id="4115a-168">删除</span><span class="sxs-lookup"><span data-stu-id="4115a-168">Delete</span></span>

* <span data-ttu-id="4115a-169">删除所有记录并级联删除其子记录。</span><span class="sxs-lookup"><span data-stu-id="4115a-169">Delete all records with cascade to child records.</span></span>
  * <span data-ttu-id="4115a-170">`BlogsPage.xaml.cs` 的 `DeleteAll_Clicked` 函数可删除 SQLite 数据库中的所有 `Blog` 记录，并级联删除 `Blog` 的所有 `Post` 子记录。</span><span class="sxs-lookup"><span data-stu-id="4115a-170">The `DeleteAll_Clicked` function of `BlogsPage.xaml.cs` deletes all the `Blog` records in the SQLite database and cascades the deletes to all of the `Blog` child `Post` records.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    blogContext.RemoveRange(blogContext.Blogs);

    await blogContext.SaveChangesAsync();
}
```

## <a name="next-steps"></a><span data-ttu-id="4115a-171">后续步骤</span><span class="sxs-lookup"><span data-stu-id="4115a-171">Next steps</span></span>

<span data-ttu-id="4115a-172">在本入门教程中，你已了解如何使用 Xamarin.Forms 应用程序通过 Entity Framework Core 访问 SQLite 数据库。</span><span class="sxs-lookup"><span data-stu-id="4115a-172">In this getting started you have learned how to use a Xamarin.Forms application to access a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="4115a-173">Xamarin 开发人员感兴趣的其他 Entity Framework Core 主题：</span><span class="sxs-lookup"><span data-stu-id="4115a-173">Other Entity Framework Core topics of interest to Xamarin developers:</span></span>

* [<span data-ttu-id="4115a-174">配置 `DbContext`</span><span class="sxs-lookup"><span data-stu-id="4115a-174">Configuring a `DbContext`</span></span>](xref:core/miscellaneous/configuring-dbcontext)
* <span data-ttu-id="4115a-175">了解有关 [LINQ 查询表达式](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)的详细信息</span><span class="sxs-lookup"><span data-stu-id="4115a-175">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
* <span data-ttu-id="4115a-176">[配置模型](xref:core/modeling/index)指定[必需项](xref:core/modeling/entity-properties#required-and-optional-properties)和[最大长度](xref:core/modeling/entity-properties#maximum-length)等内容</span><span class="sxs-lookup"><span data-stu-id="4115a-176">[Configure your model](xref:core/modeling/index) to specify things like [required](xref:core/modeling/entity-properties#required-and-optional-properties) and [maximum length](xref:core/modeling/entity-properties#maximum-length)</span></span>
