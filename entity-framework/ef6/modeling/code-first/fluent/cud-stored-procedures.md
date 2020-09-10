---
title: Code First 插入、更新和删除存储过程-EF6
description: Code First 在实体框架6中插入、更新和删除存储过程
author: divega
ms.date: 10/23/2016
ms.assetid: 9a7ae7f9-4072-4843-877d-506dd7eef576
uid: ef6/modeling/code-first/fluent/cud-stored-procedures
ms.openlocfilehash: 4db54d7199baa408017159e25ce79a9d70707c59
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618115"
---
# <a name="code-first-insert-update-and-delete-stored-procedures"></a>Code First 插入、更新和删除存储过程
> [!NOTE]
> **仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。 如果使用的是早期版本，则部分或全部信息不适用。  

默认情况下，Code First 会将所有实体配置为使用直接表访问来执行插入、更新和删除命令。 从 EF6 开始，你可以配置 Code First 模型，以便为模型中的部分或所有实体使用存储过程。  

## <a name="basic-entity-mapping"></a>基本实体映射  

可以使用熟知的 API 选择使用存储过程执行插入、更新和删除操作。  

``` csharp
modelBuilder
  .Entity<Blog>()
  .MapToStoredProcedures();
```  

这样做将导致 Code First 使用一些约定来生成数据库中的存储过程的预期形状。  

- 名为** \<type_name\> _Insert**、 ** \<type_name\> _Update**和** \<type_name\> _Delete** (的三个存储过程，例如 Blog_Insert、Blog_Update 和 Blog_Delete) 。  
- 参数名称对应于属性名称。  
  > [!NOTE]
  > 如果使用 HasColumnName ( # A1 或 Column 特性重命名给定属性的列，则此名称用于参数而不是属性名称。  
- **Insert 存储过程** 的每个属性都有一个参数，但标记为存储 (标识或计算) 中除外。 存储过程应该返回一个结果集，其中包含每个商店生成的属性的列。  
- **Update 存储过程** 的每个属性都有一个参数，但使用存储生成模式 "计算" 的标记除外。 某些并发令牌需要原始值的参数，有关详细信息，请参阅下面的 *并发标记* 部分。 存储过程应该返回一个结果集，其中包含每个计算属性的列。  
- **Delete 存储过程** 应具有实体的键值的参数 (或多个参数（如果该实体具有复合键) 。 此外，删除过程还应具有针对目标表上的任何独立关联外键的参数 (不具有实体) 中声明的相应外键属性的关系。 某些并发令牌需要原始值的参数，有关详细信息，请参阅下面的 *并发标记* 部分。  

使用以下类作为示例：  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  public string Url { get; set; }  
}
```  

默认存储过程是：  

``` SQL
CREATE PROCEDURE [dbo].[Blog_Insert]  
  @Name nvarchar(max),  
  @Url nvarchar(max)  
AS  
BEGIN
  INSERT INTO [dbo].[Blogs] ([Name], [Url])
  VALUES (@Name, @Url)

  SELECT SCOPE_IDENTITY() AS BlogId
END
CREATE PROCEDURE [dbo].[Blog_Update]  
  @BlogId int,  
  @Name nvarchar(max),  
  @Url nvarchar(max)  
AS  
  UPDATE [dbo].[Blogs]
  SET [Name] = @Name, [Url] = @Url     
  WHERE BlogId = @BlogId;
CREATE PROCEDURE [dbo].[Blog_Delete]  
  @BlogId int  
AS  
  DELETE FROM [dbo].[Blogs]
  WHERE BlogId = @BlogId
```  

### <a name="overriding-the-defaults"></a>重写默认值  

您可以覆盖默认情况下配置的部分或全部内容。  

您可以更改一个或多个存储过程的名称。 此示例仅重命名更新存储过程。  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog")));
```  

此示例将重命名所有三个存储过程。  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog"))  
     .Delete(d => d.HasName("delete_blog"))  
     .Insert(i => i.HasName("insert_blog")));
```  

在这些示例中，调用链接在一起，但你也可以使用 lambda 块语法。  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    {  
      s.Update(u => u.HasName("modify_blog"));  
      s.Delete(d => d.HasName("delete_blog"));  
      s.Insert(i => i.HasName("insert_blog"));  
    });
```  

此示例将重命名更新存储过程中的 BlogId 属性的参数。  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.BlogId, "blog_id")));
```  

这些调用都是可链且可组合的。 下面的示例将重命名所有三个存储过程及其参数。  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog")  
                   .Parameter(b => b.BlogId, "blog_id")  
                   .Parameter(b => b.Name, "blog_name")  
                   .Parameter(b => b.Url, "blog_url"))  
     .Delete(d => d.HasName("delete_blog")  
                   .Parameter(b => b.BlogId, "blog_id"))  
     .Insert(i => i.HasName("insert_blog")  
                   .Parameter(b => b.Name, "blog_name")  
                   .Parameter(b => b.Url, "blog_url")));
```  

您还可以更改包含数据库生成值的结果集中的列的名称。  

``` csharp
modelBuilder
  .Entity<Blog>()
  .MapToStoredProcedures(s =>
    s.Insert(i => i.Result(b => b.BlogId, "generated_blog_identity")));
```

``` SQL
CREATE PROCEDURE [dbo].[Blog_Insert]  
  @Name nvarchar(max),  
  @Url nvarchar(max)  
AS  
BEGIN
  INSERT INTO [dbo].[Blogs] ([Name], [Url])
  VALUES (@Name, @Url)

  SELECT SCOPE_IDENTITY() AS generated_blog_id
END
```  

## <a name="relationships-without-a-foreign-key-in-the-class-independent-associations"></a>类中没有外键的关系 (独立关联)   

在类定义中包括外键属性时，可以使用与任何其他属性相同的方式重命名相应的参数。 如果存在类中没有外键属性的关系，则默认参数名称为** \<navigation_property_name\> _ \<primary_key_name\> **。  

例如，以下类定义将导致在要插入和更新发布的存储过程中出现 Blog_BlogId 参数。  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  public string Url { get; set; }

  public List<Post> Posts { get; set; }  
}  

public class Post  
{  
  public int PostId { get; set; }  
  public string Title { get; set; }  
  public string Content { get; set; }  

  public Blog Blog { get; set; }  
}
```  

### <a name="overriding-the-defaults"></a>重写默认值  

通过向参数方法提供 primary key 属性的路径，可以更改未包含在类中的外键的参数。  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Parameter(p => p.Blog.BlogId, "blog_id")));
```  

如果依赖实体上没有导航属性 (即 没有 Post。博客属性) 然后可以使用 Association 方法标识关系的另一端，然后配置与)  (的每个键属性对应的参数。  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Navigation<Blog>(  
      b => b.Posts,  
      c => c.Parameter(b => b.BlogId, "blog_id"))));
```  

## <a name="concurrency-tokens"></a>并发标记  

更新和删除存储过程可能还需要处理并发：  

- 如果该实体包含并发标记，则该存储过程可以有选择性地包含一个 output 参数，该参数可返回受影响)  (行更新/删除的行数。 必须使用 RowsAffectedParameter 方法配置此类参数。  
默认情况下，EF 使用 ExecuteNonQuery 的返回值来确定受影响的行数。 如果在过程中执行任何逻辑，则指定 rows 受影响的 output 参数非常有用，这会导致 ExecuteNonQuery 的返回值在执行结束时从 EF 的角度) 不正确 (。  
- 对于每个并发标记，都将有一个名为** \<property_name\> _Original**的参数 (例如，Timestamp_Original ) 。 这会传递到此属性的原始值–从数据库查询时的值。  
    - 数据库计算的并发性标记（如时间戳）将只有原始值参数。  
    - 设置为并发令牌的非计算属性也会在更新过程中为新值提供一个参数。 这会对新值使用已讨论的命名约定。 此类标记的一个示例将使用博客的 URL 作为并发标记，这是必需的，因为你的代码可以将此值更新为新值 (与仅由数据库) 更新的时间戳标记不同。  

这是一个示例类，并用时间戳并发标记更新存储过程。  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  public string Url { get; set; }  
  [Timestamp]
  public byte[] Timestamp { get; set; }
}
```

``` SQL
CREATE PROCEDURE [dbo].[Blog_Update]  
  @BlogId int,  
  @Name nvarchar(max),  
  @Url nvarchar(max),
  @Timestamp_Original rowversion  
AS  
  UPDATE [dbo].[Blogs]
  SET [Name] = @Name, [Url] = @Url     
  WHERE BlogId = @BlogId AND [Timestamp] = @Timestamp_Original
```  

下面是一个示例类，并使用非计算并发标记更新存储过程。  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  [ConcurrencyCheck]
  public string Url { get; set; }  
}
```

``` SQL
CREATE PROCEDURE [dbo].[Blog_Update]  
  @BlogId int,  
  @Name nvarchar(max),  
  @Url nvarchar(max),
  @Url_Original nvarchar(max),
AS  
  UPDATE [dbo].[Blogs]
  SET [Name] = @Name, [Url] = @Url     
  WHERE BlogId = @BlogId AND [Url] = @Url_Original
```  

### <a name="overriding-the-defaults"></a>重写默认值  

您可以选择引入受影响的行参数。  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.RowsAffectedParameter("rows_affected")));
```  

对于数据库计算的并发标记（仅传递原始值），只需使用标准参数重命名机制来重命名原始值的参数。  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.Timestamp, "blog_timestamp")));
```  

对于非计算并发标记（同时传递原始值和新值），可以使用参数的重载，以便为每个参数提供名称。  

``` csharp
modelBuilder
 .Entity<Blog>()
 .MapToStoredProcedures(s => s.Update(u => u.Parameter(b => b.Url, "blog_url", "blog_original_url")));
```  

## <a name="many-to-many-relationships"></a>多对多关系  

我们将使用以下类作为此部分中的示例。  

``` csharp
public class Post  
{  
  public int PostId { get; set; }  
  public string Title { get; set; }  
  public string Content { get; set; }  

  public List<Tag> Tags { get; set; }  
}  

public class Tag  
{  
  public int TagId { get; set; }  
  public string TagName { get; set; }  

  public List<Post> Posts { get; set; }  
}
```  

多对多关系可以映射到具有以下语法的存储过程。  

``` csharp
modelBuilder  
  .Entity<Post>()  
  .HasMany(p => p.Tags)  
  .WithMany(t => t.Posts)  
  .MapToStoredProcedures();
```  

如果未提供其他配置，则默认情况下使用以下存储过程形状。  

- 名为** \<type_one\> \<type_two\> _Insert**和** \<type_one\> \<type_two\> _Delete**的两个存储过程 (例如，PostTag_Insert 和 PostTag_Delete) 。  
- 对于每种类型，参数将是 (s) 的键值。 每个参数的名称为** \<type_name\> _ \<property_name\> ** (例如，Post_PostId 和 Tag_TagId) 。

下面是 insert 和 update 存储过程的示例。  

``` SQL
CREATE PROCEDURE [dbo].[PostTag_Insert]  
  @Post_PostId int,  
  @Tag_TagId int  
AS  
  INSERT INTO [dbo].[Post_Tags] (Post_PostId, Tag_TagId)   
  VALUES (@Post_PostId, @Tag_TagId)
CREATE PROCEDURE [dbo].[PostTag_Delete]  
  @Post_PostId int,  
  @Tag_TagId int  
AS  
  DELETE FROM [dbo].[Post_Tags]    
  WHERE Post_PostId = @Post_PostId AND Tag_TagId = @Tag_TagId
```  

### <a name="overriding-the-defaults"></a>重写默认值  

可以采用类似的方式配置过程和参数名称以与实体存储过程类似。  

``` csharp
modelBuilder  
  .Entity<Post>()  
  .HasMany(p => p.Tags)  
  .WithMany(t => t.Posts)  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.HasName("add_post_tag")  
                   .LeftKeyParameter(p => p.PostId, "post_id")  
                   .RightKeyParameter(t => t.TagId, "tag_id"))  
     .Delete(d => d.HasName("remove_post_tag")  
                   .LeftKeyParameter(p => p.PostId, "post_id")  
                   .RightKeyParameter(t => t.TagId, "tag_id")));
```  
