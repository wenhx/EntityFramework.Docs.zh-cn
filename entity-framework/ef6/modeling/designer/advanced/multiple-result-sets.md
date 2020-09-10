---
title: 具有多个结果集的存储过程-EF6
description: 在实体框架6中包含多个结果集的存储过程
author: divega
ms.date: 10/23/2016
ms.assetid: 1b3797f9-cd3d-4752-a55e-47b84b399dc1
uid: ef6/modeling/designer/advanced/multiple-result-sets
ms.openlocfilehash: 39b8ae171b7cee263554b6ff94bed6bd8f1e0c39
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620583"
---
# <a name="stored-procedures-with-multiple-result-sets"></a><span data-ttu-id="4e464-103">包含多个结果集的存储过程</span><span class="sxs-lookup"><span data-stu-id="4e464-103">Stored Procedures with Multiple Result Sets</span></span>
<span data-ttu-id="4e464-104">有时，在使用存储过程时，您需要返回多个结果集。</span><span class="sxs-lookup"><span data-stu-id="4e464-104">Sometimes when using stored procedures you will need to return more than one result set.</span></span> <span data-ttu-id="4e464-105">此方案通常用于减少构成单个屏幕所需的数据库往返次数。</span><span class="sxs-lookup"><span data-stu-id="4e464-105">This scenario is commonly used to reduce the number of database round trips required to compose a single screen.</span></span><span data-ttu-id="4e464-106">在 EF5 之前，实体框架允许调用存储过程，但只将第一个结果集返回给调用代码。</span><span class="sxs-lookup"><span data-stu-id="4e464-106"> Prior to EF5, Entity Framework would allow the stored procedure to be called but would only return the first result set to the calling code.</span></span>

<span data-ttu-id="4e464-107">本文将介绍两种方法，可用于从实体框架中的存储过程访问多个结果集。</span><span class="sxs-lookup"><span data-stu-id="4e464-107">This article will show you two ways that you can use to access more than one result set from a stored procedure in Entity Framework.</span></span> <span data-ttu-id="4e464-108">一个只使用代码，并处理代码 first 和 EF 设计器，另一个仅适用于 EF 设计器的。</span><span class="sxs-lookup"><span data-stu-id="4e464-108">One that uses just code and works with both Code first and the EF Designer and one that only works with the EF Designer.</span></span> <span data-ttu-id="4e464-109">此方面的工具和 API 支持应在实体框架的未来版本中得以改善。</span><span class="sxs-lookup"><span data-stu-id="4e464-109">The tooling and API support for this should improve in future versions of Entity Framework.</span></span>

## <a name="model"></a><span data-ttu-id="4e464-110">型号</span><span class="sxs-lookup"><span data-stu-id="4e464-110">Model</span></span>

<span data-ttu-id="4e464-111">本文中的示例使用的是基本的博客和文章模型，其中的博客包含多篇文章，张贴内容属于单个博客。</span><span class="sxs-lookup"><span data-stu-id="4e464-111">The examples in this article use a basic Blog and Posts model where a blog has many posts and a post belongs to a single blog.</span></span> <span data-ttu-id="4e464-112">我们将使用数据库中的存储过程，该存储过程返回所有博客和帖子，如下所示：</span><span class="sxs-lookup"><span data-stu-id="4e464-112">We will use a stored procedure in the database that returns all blogs and posts, something like this:</span></span>

``` SQL
    CREATE PROCEDURE [dbo].[GetAllBlogsAndPosts]
    AS
        SELECT * FROM dbo.Blogs
        SELECT * FROM dbo.Posts
```

## <a name="accessing-multiple-result-sets-with-code"></a><span data-ttu-id="4e464-113">用代码访问多个结果集</span><span class="sxs-lookup"><span data-stu-id="4e464-113">Accessing Multiple Result Sets with Code</span></span>

<span data-ttu-id="4e464-114">我们可以执行使用代码发出原始 SQL 命令，以执行存储过程。</span><span class="sxs-lookup"><span data-stu-id="4e464-114">We can execute use code to issue a raw SQL command to execute our stored procedure.</span></span> <span data-ttu-id="4e464-115">此方法的优点是它适用于代码 first 和 EF 设计器。</span><span class="sxs-lookup"><span data-stu-id="4e464-115">The advantage of this approach is that it works with both Code first and the EF Designer.</span></span>

<span data-ttu-id="4e464-116">为了获得多个结果集，我们需要使用 IObjectContextAdapter 接口删除 ObjectContext API。</span><span class="sxs-lookup"><span data-stu-id="4e464-116">In order to get multiple result sets working we need to drop to the ObjectContext API by using the IObjectContextAdapter interface.</span></span>

<span data-ttu-id="4e464-117">获得 ObjectContext 后，我们可以使用转换方法将存储过程的结果转换为可在 EF 中跟踪和使用的实体。</span><span class="sxs-lookup"><span data-stu-id="4e464-117">Once we have an ObjectContext then we can use the Translate method to translate the results of our stored procedure into entities that can be tracked and used in EF as normal.</span></span> <span data-ttu-id="4e464-118">下面的代码示例演示如何执行此操作。</span><span class="sxs-lookup"><span data-stu-id="4e464-118">The following code sample demonstrates this in action.</span></span>

``` csharp
    using (var db = new BloggingContext())
    {
        // If using Code First we need to make sure the model is built before we open the connection
        // This isn't required for models created with the EF Designer
        db.Database.Initialize(force: false);

        // Create a SQL command to execute the sproc
        var cmd = db.Database.Connection.CreateCommand();
        cmd.CommandText = "[dbo].[GetAllBlogsAndPosts]";

        try
        {

            db.Database.Connection.Open();
            // Run the sproc
            var reader = cmd.ExecuteReader();

            // Read Blogs from the first result set
            var blogs = ((IObjectContextAdapter)db)
                .ObjectContext
                .Translate<Blog>(reader, "Blogs", MergeOption.AppendOnly);   


            foreach (var item in blogs)
            {
                Console.WriteLine(item.Name);
            }        

            // Move to second result set and read Posts
            reader.NextResult();
            var posts = ((IObjectContextAdapter)db)
                .ObjectContext
                .Translate<Post>(reader, "Posts", MergeOption.AppendOnly);


            foreach (var item in posts)
            {
                Console.WriteLine(item.Title);
            }
        }
        finally
        {
            db.Database.Connection.Close();
        }
    }
```

<span data-ttu-id="4e464-119">转换方法接受我们在执行过程、EntitySet 名称和 MergeOption 时收到的读取器。</span><span class="sxs-lookup"><span data-stu-id="4e464-119">The Translate method accepts the reader that we received when we executed the procedure, an EntitySet name, and a MergeOption.</span></span> <span data-ttu-id="4e464-120">EntitySet 名称将与派生上下文上的 DbSet 属性相同。</span><span class="sxs-lookup"><span data-stu-id="4e464-120">The EntitySet name will be the same as the DbSet property on your derived context.</span></span> <span data-ttu-id="4e464-121">MergeOption 枚举控制在内存中已存在相同的实体时如何处理结果。</span><span class="sxs-lookup"><span data-stu-id="4e464-121">The MergeOption enum controls how results are handled if the same entity already exists in memory.</span></span>

<span data-ttu-id="4e464-122">在这里，我们将循环访问博客的集合，然后调用 NextResult，这对于上述代码非常重要，因为在移动到下一个结果集之前必须使用第一个结果集。</span><span class="sxs-lookup"><span data-stu-id="4e464-122">Here we iterate through the collection of blogs before we call NextResult, this is important given the above code because the first result set must be consumed before moving to the next result set.</span></span>

<span data-ttu-id="4e464-123">一旦调用这两个转换方法，EF 就会以与任何其他实体相同的方式跟踪博客和公告实体，因此可以修改或删除它们并将其保存为正常。</span><span class="sxs-lookup"><span data-stu-id="4e464-123">Once the two translate methods are called then the Blog and Post entities are tracked by EF the same way as any other entity and so can be modified or deleted and saved as normal.</span></span>

>[!NOTE]
> <span data-ttu-id="4e464-124">当使用转换方法创建实体时，EF 不会考虑任何映射。</span><span class="sxs-lookup"><span data-stu-id="4e464-124">EF does not take any mapping into account when it creates entities using the Translate method.</span></span> <span data-ttu-id="4e464-125">它只是将结果集中的列名称与类的属性名进行匹配。</span><span class="sxs-lookup"><span data-stu-id="4e464-125">It will simply match column names in the result set with property names on your classes.</span></span>

>[!NOTE]
> <span data-ttu-id="4e464-126">这种情况下，如果启用了延迟加载，则访问其中一个博客实体上的 "发布" 属性后，EF 将连接到数据库以延迟加载所有发布，即使我们已经加载了所有发布。</span><span class="sxs-lookup"><span data-stu-id="4e464-126">That if you have lazy loading enabled, accessing the posts property on one of the blog entities then EF will connect to the database to lazily load all posts, even though we have already loaded them all.</span></span> <span data-ttu-id="4e464-127">这是因为 EF 无法知道是否已加载所有发布或数据库中是否存在其他内容。</span><span class="sxs-lookup"><span data-stu-id="4e464-127">This is because EF cannot know whether or not you have loaded all posts or if there are more in the database.</span></span> <span data-ttu-id="4e464-128">如果要避免这种情况，则需要禁用延迟加载。</span><span class="sxs-lookup"><span data-stu-id="4e464-128">If you want to avoid this then you will need to disable lazy loading.</span></span>

## <a name="multiple-result-sets-with-configured-in-edmx"></a><span data-ttu-id="4e464-129">在 EDMX 中配置的多个结果集</span><span class="sxs-lookup"><span data-stu-id="4e464-129">Multiple Result Sets with Configured in EDMX</span></span>

>[!NOTE]
> <span data-ttu-id="4e464-130">必须针对 .NET Framework 4.5，才能在 EDMX 中配置多个结果集。</span><span class="sxs-lookup"><span data-stu-id="4e464-130">You must target .NET Framework 4.5 to be able to configure multiple result sets in EDMX.</span></span> <span data-ttu-id="4e464-131">如果面向的是 .NET 4.0，则可以使用上一节中所示的基于代码的方法。</span><span class="sxs-lookup"><span data-stu-id="4e464-131">If you are targeting .NET 4.0 you can use the code-based method shown in the previous section.</span></span>

<span data-ttu-id="4e464-132">如果使用的是 EF 设计器，则还可以修改模型，使其了解将返回的不同结果集。</span><span class="sxs-lookup"><span data-stu-id="4e464-132">If you are using the EF Designer, you can also modify your model so that it knows about the different result sets that will be returned.</span></span> <span data-ttu-id="4e464-133">在手头之前需要了解的一点是，该工具不是多个结果集感知，因此您需要手动编辑该 edmx 文件。</span><span class="sxs-lookup"><span data-stu-id="4e464-133">One thing to know before hand is that the tooling is not multiple result set aware, so you will need to manually edit the edmx file.</span></span> <span data-ttu-id="4e464-134">编辑此类 edmx 文件的操作将起作用，但它也会破坏 VS 中模型的验证。</span><span class="sxs-lookup"><span data-stu-id="4e464-134">Editing the edmx file like this will work but it will also break the validation of the model in VS.</span></span> <span data-ttu-id="4e464-135">如果验证模型，将始终会出现错误。</span><span class="sxs-lookup"><span data-stu-id="4e464-135">So if you validate your model you will always get errors.</span></span>

-   <span data-ttu-id="4e464-136">要执行此操作，您需要将该存储过程添加到您的模型中，就像对单个结果集查询执行此操作一样。</span><span class="sxs-lookup"><span data-stu-id="4e464-136">In order to do this you need to add the stored procedure to your model as you would for a single result set query.</span></span>
-   <span data-ttu-id="4e464-137">完成后，需要右键单击模型，然后选择 "**打开方式**"。</span><span class="sxs-lookup"><span data-stu-id="4e464-137">Once you have this then you need to right click on your model and select **Open With..**</span></span> <span data-ttu-id="4e464-138">then **Xml**</span><span class="sxs-lookup"><span data-stu-id="4e464-138">then **Xml**</span></span>

    ![打开为](~/ef6/media/openas.png)

<span data-ttu-id="4e464-140">将模型作为 XML 打开后，需要执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="4e464-140">Once you have the model opened as XML then you need to do the following steps:</span></span>

-   <span data-ttu-id="4e464-141">在模型中查找复杂类型和函数导入：</span><span class="sxs-lookup"><span data-stu-id="4e464-141">Find the complex type and function import in your model:</span></span>

``` xml
    <!-- CSDL content -->
    <edmx:ConceptualModels>

    ...

      <FunctionImport Name="GetAllBlogsAndPosts" ReturnType="Collection(BlogModel.GetAllBlogsAndPosts_Result)" />

    ...

      <ComplexType Name="GetAllBlogsAndPosts_Result">
        <Property Type="Int32" Name="BlogId" Nullable="false" />
        <Property Type="String" Name="Name" Nullable="false" MaxLength="255" />
        <Property Type="String" Name="Description" Nullable="true" />
      </ComplexType>

    ...

    </edmx:ConceptualModels>
```

 

-   <span data-ttu-id="4e464-142">删除复杂类型</span><span class="sxs-lookup"><span data-stu-id="4e464-142">Remove the complex type</span></span>
-   <span data-ttu-id="4e464-143">更新函数 import，使其映射到你的实体，在本例中，它将如下所示：</span><span class="sxs-lookup"><span data-stu-id="4e464-143">Update the function import so that it maps to your entities, in our case it will look like the following:</span></span>

``` xml
    <FunctionImport Name="GetAllBlogsAndPosts">
      <ReturnType EntitySet="Blogs" Type="Collection(BlogModel.Blog)" />
      <ReturnType EntitySet="Posts" Type="Collection(BlogModel.Post)" />
    </FunctionImport>
```

<span data-ttu-id="4e464-144">这会告知模型，存储过程将返回两个集合：一个博客项和一个发布项。</span><span class="sxs-lookup"><span data-stu-id="4e464-144">This tells the model that the stored procedure will return two collections, one of blog entries and one of post entries.</span></span>

-   <span data-ttu-id="4e464-145">查找函数映射元素：</span><span class="sxs-lookup"><span data-stu-id="4e464-145">Find the function mapping element:</span></span>

``` xml
    <!-- C-S mapping content -->
    <edmx:Mappings>

    ...

      <FunctionImportMapping FunctionImportName="GetAllBlogsAndPosts" FunctionName="BlogModel.Store.GetAllBlogsAndPosts">
        <ResultMapping>
          <ComplexTypeMapping TypeName="BlogModel.GetAllBlogsAndPosts_Result">
            <ScalarProperty Name="BlogId" ColumnName="BlogId" />
            <ScalarProperty Name="Name" ColumnName="Name" />
            <ScalarProperty Name="Description" ColumnName="Description" />
          </ComplexTypeMapping>
        </ResultMapping>
      </FunctionImportMapping>

    ...

    </edmx:Mappings>
```

-   <span data-ttu-id="4e464-146">将结果映射替换为每个要返回的实体，如下所示：</span><span class="sxs-lookup"><span data-stu-id="4e464-146">Replace the result mapping with one for each entity being returned, such as the following:</span></span>

``` xml
    <ResultMapping>
      <EntityTypeMapping TypeName ="BlogModel.Blog">
        <ScalarProperty Name="BlogId" ColumnName="BlogId" />
        <ScalarProperty Name="Name" ColumnName="Name" />
        <ScalarProperty Name="Description" ColumnName="Description" />
      </EntityTypeMapping>
    </ResultMapping>
    <ResultMapping>
      <EntityTypeMapping TypeName="BlogModel.Post">
        <ScalarProperty Name="BlogId" ColumnName="BlogId" />
        <ScalarProperty Name="PostId" ColumnName="PostId"/>
        <ScalarProperty Name="Title" ColumnName="Title" />
        <ScalarProperty Name="Text" ColumnName="Text" />
      </EntityTypeMapping>
    </ResultMapping>
```

<span data-ttu-id="4e464-147">还可以将结果集映射到复杂类型，如默认创建的类型。</span><span class="sxs-lookup"><span data-stu-id="4e464-147">It is also possible to map the result sets to complex types, such as the one created by default.</span></span> <span data-ttu-id="4e464-148">为此，您创建了一个新的复杂类型，而不是删除它们，并使用在上述示例中使用了实体名称的任何位置的复杂类型。</span><span class="sxs-lookup"><span data-stu-id="4e464-148">To do this you create a new complex type, instead of removing them, and use the complex types everywhere that you had used the entity names in the examples above.</span></span>

<span data-ttu-id="4e464-149">更改这些映射后，你可以保存模型并执行以下代码以使用该存储过程：</span><span class="sxs-lookup"><span data-stu-id="4e464-149">Once these mappings have been changed then you can save the model and execute the following code to use the stored procedure:</span></span>

``` csharp
    using (var db = new BlogEntities())
    {
        var results = db.GetAllBlogsAndPosts();

        foreach (var result in results)
        {
            Console.WriteLine("Blog: " + result.Name);
        }

        var posts = results.GetNextResult<Post>();

        foreach (var result in posts)
        {
            Console.WriteLine("Post: " + result.Title);
        }

        Console.ReadLine();
    }
```

>[!NOTE]
> <span data-ttu-id="4e464-150">如果手动编辑模型的 edmx 文件，则从数据库重新生成模型时，它将被覆盖。</span><span class="sxs-lookup"><span data-stu-id="4e464-150">If you manually edit the edmx file for your model it will be overwritten if you ever regenerate the model from the database.</span></span>

## <a name="summary"></a><span data-ttu-id="4e464-151">总结</span><span class="sxs-lookup"><span data-stu-id="4e464-151">Summary</span></span>

<span data-ttu-id="4e464-152">这里，我们介绍了两种不同的方法来使用实体框架访问多个结果集。</span><span class="sxs-lookup"><span data-stu-id="4e464-152">Here we have shown two different methods of accessing multiple result sets using Entity Framework.</span></span> <span data-ttu-id="4e464-153">这两种方法都是相同的，具体取决于你的情况和首选项，你应选择最适合你的环境的方法。</span><span class="sxs-lookup"><span data-stu-id="4e464-153">Both of them are equally valid depending on your situation and preferences and you should choose the one that seems best for your circumstances.</span></span> <span data-ttu-id="4e464-154">它计划在未来版本的实体框架中对多个结果集的支持进行改进，并且不再需要执行本文档中的步骤。</span><span class="sxs-lookup"><span data-stu-id="4e464-154">It is planned that the support for multiple result sets will be improved in future versions of Entity Framework and that performing the steps in this document will no longer be necessary.</span></span>
