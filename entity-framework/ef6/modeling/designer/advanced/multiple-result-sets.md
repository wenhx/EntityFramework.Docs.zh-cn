---
title: 具有多个结果集的存储过程-EF6
description: 在实体框架6中包含多个结果集的存储过程
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/multiple-result-sets
ms.openlocfilehash: 8c80e2b8c861a763b7f63ea2523194028498dace
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066273"
---
# <a name="stored-procedures-with-multiple-result-sets"></a>包含多个结果集的存储过程
有时，在使用存储过程时，您需要返回多个结果集。 此方案通常用于减少构成单个屏幕所需的数据库往返次数。在 EF5 之前，实体框架允许调用存储过程，但只将第一个结果集返回给调用代码。

本文将介绍两种方法，可用于从实体框架中的存储过程访问多个结果集。 一个只使用代码，并处理代码 first 和 EF 设计器，另一个仅适用于 EF 设计器的。 此方面的工具和 API 支持应在实体框架的未来版本中得以改善。

## <a name="model"></a>“模型”

本文中的示例使用的是基本的博客和文章模型，其中的博客包含多篇文章，张贴内容属于单个博客。 我们将使用数据库中的存储过程，该存储过程返回所有博客和帖子，如下所示：

``` SQL
    CREATE PROCEDURE [dbo].[GetAllBlogsAndPosts]
    AS
        SELECT * FROM dbo.Blogs
        SELECT * FROM dbo.Posts
```

## <a name="accessing-multiple-result-sets-with-code"></a>用代码访问多个结果集

我们可以执行使用代码发出原始 SQL 命令，以执行存储过程。 此方法的优点是它适用于代码 first 和 EF 设计器。

为了获得多个结果集，我们需要使用 IObjectContextAdapter 接口删除 ObjectContext API。

获得 ObjectContext 后，我们可以使用转换方法将存储过程的结果转换为可在 EF 中跟踪和使用的实体。 下面的代码示例演示如何执行此操作。

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

转换方法接受我们在执行过程、EntitySet 名称和 MergeOption 时收到的读取器。 EntitySet 名称将与派生上下文上的 DbSet 属性相同。 MergeOption 枚举控制在内存中已存在相同的实体时如何处理结果。

在这里，我们将循环访问博客的集合，然后调用 NextResult，这对于上述代码非常重要，因为在移动到下一个结果集之前必须使用第一个结果集。

一旦调用这两个转换方法，EF 就会以与任何其他实体相同的方式跟踪博客和公告实体，因此可以修改或删除它们并将其保存为正常。

>[!NOTE]
> 当使用转换方法创建实体时，EF 不会考虑任何映射。 它只是将结果集中的列名称与类的属性名进行匹配。

>[!NOTE]
> 这种情况下，如果启用了延迟加载，则访问其中一个博客实体上的 "发布" 属性后，EF 将连接到数据库以延迟加载所有发布，即使我们已经加载了所有发布。 这是因为 EF 无法知道是否已加载所有发布或数据库中是否存在其他内容。 如果要避免这种情况，则需要禁用延迟加载。

## <a name="multiple-result-sets-with-configured-in-edmx"></a>在 EDMX 中配置的多个结果集

>[!NOTE]
> 必须针对 .NET Framework 4.5，才能在 EDMX 中配置多个结果集。 如果面向的是 .NET 4.0，则可以使用上一节中所示的基于代码的方法。

如果使用的是 EF 设计器，则还可以修改模型，使其了解将返回的不同结果集。 在手头之前需要了解的一点是，该工具不是多个结果集感知，因此您需要手动编辑该 edmx 文件。 编辑此类 edmx 文件的操作将起作用，但它也会破坏 VS 中模型的验证。 如果验证模型，将始终会出现错误。

-   要执行此操作，您需要将该存储过程添加到您的模型中，就像对单个结果集查询执行此操作一样。
-   完成后，需要右键单击模型，然后选择 "**打开方式**"。 then **Xml**

    ![打开为](~/ef6/media/openas.png)

将模型作为 XML 打开后，需要执行以下步骤：

-   在模型中查找复杂类型和函数导入：

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

 

-   删除复杂类型
-   更新函数 import，使其映射到你的实体，在本例中，它将如下所示：

``` xml
    <FunctionImport Name="GetAllBlogsAndPosts">
      <ReturnType EntitySet="Blogs" Type="Collection(BlogModel.Blog)" />
      <ReturnType EntitySet="Posts" Type="Collection(BlogModel.Post)" />
    </FunctionImport>
```

这会告知模型，存储过程将返回两个集合：一个博客项和一个发布项。

-   查找函数映射元素：

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

-   将结果映射替换为每个要返回的实体，如下所示：

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

还可以将结果集映射到复杂类型，如默认创建的类型。 为此，您创建了一个新的复杂类型，而不是删除它们，并使用在上述示例中使用了实体名称的任何位置的复杂类型。

更改这些映射后，你可以保存模型并执行以下代码以使用该存储过程：

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
> 如果手动编辑模型的 edmx 文件，则从数据库重新生成模型时，它将被覆盖。

## <a name="summary"></a>摘要

这里，我们介绍了两种不同的方法来使用实体框架访问多个结果集。 这两种方法都是相同的，具体取决于你的情况和首选项，你应选择最适合你的环境的方法。 它计划在未来版本的实体框架中对多个结果集的支持进行改进，并且不再需要执行本文档中的步骤。
