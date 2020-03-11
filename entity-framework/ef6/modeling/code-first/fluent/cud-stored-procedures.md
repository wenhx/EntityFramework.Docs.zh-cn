---
title: Code First 插入、更新和删除存储过程-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9a7ae7f9-4072-4843-877d-506dd7eef576
ms.openlocfilehash: bfc56671814aec1965ac054ff901297e5cdbbecb
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415771"
---
# <a name="code-first-insert-update-and-delete-stored-procedures"></a><span data-ttu-id="6c34e-102">Code First 插入、更新和删除存储过程</span><span class="sxs-lookup"><span data-stu-id="6c34e-102">Code First Insert, Update, and Delete Stored Procedures</span></span>
> [!NOTE]
> <span data-ttu-id="6c34e-103">**仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。</span><span class="sxs-lookup"><span data-stu-id="6c34e-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="6c34e-104">如果使用的是早期版本，则部分或全部信息不适用。</span><span class="sxs-lookup"><span data-stu-id="6c34e-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="6c34e-105">默认情况下，Code First 会将所有实体配置为使用直接表访问来执行插入、更新和删除命令。</span><span class="sxs-lookup"><span data-stu-id="6c34e-105">By default, Code First will configure all entities to perform insert, update and delete commands using direct table access.</span></span> <span data-ttu-id="6c34e-106">从 EF6 开始，你可以配置 Code First 模型，以便为模型中的部分或所有实体使用存储过程。</span><span class="sxs-lookup"><span data-stu-id="6c34e-106">Starting in EF6 you can configure your Code First model to use stored procedures for some or all entities in your model.</span></span>  

## <a name="basic-entity-mapping"></a><span data-ttu-id="6c34e-107">基本实体映射</span><span class="sxs-lookup"><span data-stu-id="6c34e-107">Basic Entity Mapping</span></span>  

<span data-ttu-id="6c34e-108">可以使用熟知的 API 选择使用存储过程执行插入、更新和删除操作。</span><span class="sxs-lookup"><span data-stu-id="6c34e-108">You can opt into using stored procedures for insert, update and delete using the Fluent API.</span></span>  

``` csharp
modelBuilder
  .Entity<Blog>()
  .MapToStoredProcedures();
```  

<span data-ttu-id="6c34e-109">这样做将导致 Code First 使用一些约定来生成数据库中的存储过程的预期形状。</span><span class="sxs-lookup"><span data-stu-id="6c34e-109">Doing this will cause Code First to use some conventions to build the expected shape of the stored procedures in the database.</span></span>  

- <span data-ttu-id="6c34e-110">三个名为 **\<type_name\>_Insert**\<**type_name\>_Update**\<type_name\>_Delete **Blog_Insert Blog_Update Blog_Delete**的存储过程。</span><span class="sxs-lookup"><span data-stu-id="6c34e-110">Three stored procedures named **\<type_name\>_Insert**, **\<type_name\>_Update** and **\<type_name\>_Delete** (for example, Blog_Insert, Blog_Update and Blog_Delete).</span></span>  
- <span data-ttu-id="6c34e-111">参数名称对应于属性名称。</span><span class="sxs-lookup"><span data-stu-id="6c34e-111">Parameter names correspond to the property names.</span></span>  
  > [!NOTE]
  > <span data-ttu-id="6c34e-112">如果使用 HasColumnName （）或 Column 特性重命名给定属性的列，则此名称用于参数而不是属性名称。</span><span class="sxs-lookup"><span data-stu-id="6c34e-112">If you use HasColumnName() or the Column attribute to rename the column for a given property then this name is used for parameters instead of the property name.</span></span>  
- <span data-ttu-id="6c34e-113">**Insert 存储过程**将为每个属性提供一个参数，但标记为 "生成的存储" 的属性除外（标识或计算）。</span><span class="sxs-lookup"><span data-stu-id="6c34e-113">**The insert stored procedure** will have a parameter for every property, except for those marked as store generated (identity or computed).</span></span> <span data-ttu-id="6c34e-114">存储过程应该返回一个结果集，其中包含每个商店生成的属性的列。</span><span class="sxs-lookup"><span data-stu-id="6c34e-114">The stored procedure should return a result set with a column for each store generated property.</span></span>  
- <span data-ttu-id="6c34e-115">**Update 存储过程**的每个属性都有一个参数，但使用存储生成模式 "计算" 的标记除外。</span><span class="sxs-lookup"><span data-stu-id="6c34e-115">**The update stored procedure** will have a parameter for every property, except for those marked with a store generated pattern of 'Computed'.</span></span> <span data-ttu-id="6c34e-116">某些并发令牌需要原始值的参数，有关详细信息，请参阅下面的*并发标记*部分。</span><span class="sxs-lookup"><span data-stu-id="6c34e-116">Some concurrency tokens require a parameter for the original value, see the *Concurrency Tokens* section below for details.</span></span> <span data-ttu-id="6c34e-117">存储过程应该返回一个结果集，其中包含每个计算属性的列。</span><span class="sxs-lookup"><span data-stu-id="6c34e-117">The stored procedure should return a result set with a column for each computed property.</span></span>  
- <span data-ttu-id="6c34e-118">**Delete 存储过程**应具有实体的键值的参数（如果该实体具有组合键，则为多个参数）。</span><span class="sxs-lookup"><span data-stu-id="6c34e-118">**The delete stored procedure** should have a parameter for the key value of the entity (or multiple parameters if the entity has a composite key).</span></span> <span data-ttu-id="6c34e-119">此外，删除过程还应包含目标表上的任何独立关联外键的参数（实体中没有声明的相应外键属性的关系）。</span><span class="sxs-lookup"><span data-stu-id="6c34e-119">Additionally, the delete procedure should also have parameters for any independent association foreign keys on the target table (relationships that do not have corresponding foreign key properties declared in the entity).</span></span> <span data-ttu-id="6c34e-120">某些并发令牌需要原始值的参数，有关详细信息，请参阅下面的*并发标记*部分。</span><span class="sxs-lookup"><span data-stu-id="6c34e-120">Some concurrency tokens require a parameter for the original value, see the *Concurrency Tokens* section below for details.</span></span>  

<span data-ttu-id="6c34e-121">使用以下类作为示例：</span><span class="sxs-lookup"><span data-stu-id="6c34e-121">Using the following class as an example:</span></span>  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  public string Url { get; set; }  
}
```  

<span data-ttu-id="6c34e-122">默认存储过程是：</span><span class="sxs-lookup"><span data-stu-id="6c34e-122">The default stored procedures would be:</span></span>  

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

### <a name="overriding-the-defaults"></a><span data-ttu-id="6c34e-123">重写默认值</span><span class="sxs-lookup"><span data-stu-id="6c34e-123">Overriding the Defaults</span></span>  

<span data-ttu-id="6c34e-124">您可以覆盖默认情况下配置的部分或全部内容。</span><span class="sxs-lookup"><span data-stu-id="6c34e-124">You can override part or all of what was configured by default.</span></span>  

<span data-ttu-id="6c34e-125">您可以更改一个或多个存储过程的名称。</span><span class="sxs-lookup"><span data-stu-id="6c34e-125">You can change the name of one or more stored procedures.</span></span> <span data-ttu-id="6c34e-126">此示例仅重命名更新存储过程。</span><span class="sxs-lookup"><span data-stu-id="6c34e-126">This example renames the update stored procedure only.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog")));
```  

<span data-ttu-id="6c34e-127">此示例将重命名所有三个存储过程。</span><span class="sxs-lookup"><span data-stu-id="6c34e-127">This example renames all three stored procedures.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog"))  
     .Delete(d => d.HasName("delete_blog"))  
     .Insert(i => i.HasName("insert_blog")));
```  

<span data-ttu-id="6c34e-128">在这些示例中，调用链接在一起，但你也可以使用 lambda 块语法。</span><span class="sxs-lookup"><span data-stu-id="6c34e-128">In these examples the calls are chained together, but you can also use lambda block syntax.</span></span>  

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

<span data-ttu-id="6c34e-129">此示例将重命名更新存储过程中的 BlogId 属性的参数。</span><span class="sxs-lookup"><span data-stu-id="6c34e-129">This example renames the parameter for the BlogId property on the update stored procedure.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.BlogId, "blog_id")));
```  

<span data-ttu-id="6c34e-130">这些调用都是可链且可组合的。</span><span class="sxs-lookup"><span data-stu-id="6c34e-130">These calls are all chainable and composable.</span></span> <span data-ttu-id="6c34e-131">下面的示例将重命名所有三个存储过程及其参数。</span><span class="sxs-lookup"><span data-stu-id="6c34e-131">Here is an example that renames all three stored procedures and their parameters.</span></span>  

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

<span data-ttu-id="6c34e-132">您还可以更改包含数据库生成值的结果集中的列的名称。</span><span class="sxs-lookup"><span data-stu-id="6c34e-132">You can also change the name of the columns in the result set that contains database generated values.</span></span>  

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

## <a name="relationships-without-a-foreign-key-in-the-class-independent-associations"></a><span data-ttu-id="6c34e-133">类中没有外键的关系（独立关联）</span><span class="sxs-lookup"><span data-stu-id="6c34e-133">Relationships Without a Foreign Key in the Class (Independent Associations)</span></span>  

<span data-ttu-id="6c34e-134">在类定义中包括外键属性时，可以使用与任何其他属性相同的方式重命名相应的参数。</span><span class="sxs-lookup"><span data-stu-id="6c34e-134">When a foreign key property is included in the class definition, the corresponding parameter can be renamed in the same way as any other property.</span></span> <span data-ttu-id="6c34e-135">如果存在类中没有外键属性的关系，则默认参数名称为 **\<navigation_property_name\>_\<primary_key_name\>** 。</span><span class="sxs-lookup"><span data-stu-id="6c34e-135">When a relationship exists without a foreign key property in the class, the default parameter name is **\<navigation_property_name\>_\<primary_key_name\>**.</span></span>  

<span data-ttu-id="6c34e-136">例如，以下类定义将导致在要插入和更新发布的存储过程中出现 Blog_BlogId 参数。</span><span class="sxs-lookup"><span data-stu-id="6c34e-136">For example, the following class definitions would result in a Blog_BlogId parameter being expected in the stored procedures to insert and update Posts.</span></span>  

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

### <a name="overriding-the-defaults"></a><span data-ttu-id="6c34e-137">重写默认值</span><span class="sxs-lookup"><span data-stu-id="6c34e-137">Overriding the Defaults</span></span>  

<span data-ttu-id="6c34e-138">通过向参数方法提供 primary key 属性的路径，可以更改未包含在类中的外键的参数。</span><span class="sxs-lookup"><span data-stu-id="6c34e-138">You can change parameters for foreign keys that are not included in the class by supplying the path to the primary key property to the Parameter method.</span></span>  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Parameter(p => p.Blog.BlogId, "blog_id")));
```  

<span data-ttu-id="6c34e-139">如果依赖实体上没有导航属性（即</span><span class="sxs-lookup"><span data-stu-id="6c34e-139">If you don’t have a navigation property on the dependent entity (i.e</span></span> <span data-ttu-id="6c34e-140">不是 Post。博客属性）然后，可以使用 Association 方法标识关系的另一端，然后配置与每个键属性对应的参数。</span><span class="sxs-lookup"><span data-stu-id="6c34e-140">no Post.Blog property) then you can use the Association method to identify the other end of the relationship and then configure the parameters that correspond to each of the key property(s).</span></span>  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Navigation<Blog>(  
      b => b.Posts,  
      c => c.Parameter(b => b.BlogId, "blog_id"))));
```  

## <a name="concurrency-tokens"></a><span data-ttu-id="6c34e-141">并发标记</span><span class="sxs-lookup"><span data-stu-id="6c34e-141">Concurrency Tokens</span></span>  

<span data-ttu-id="6c34e-142">更新和删除存储过程可能还需要处理并发：</span><span class="sxs-lookup"><span data-stu-id="6c34e-142">Update and delete stored procedures may also need to deal with concurrency:</span></span>  

- <span data-ttu-id="6c34e-143">如果实体包含并发标记，则存储过程可以有选择性地包含一个 output 参数，该参数返回更新/删除的行数（受影响的行数）。</span><span class="sxs-lookup"><span data-stu-id="6c34e-143">If the entity contains concurrency tokens, the stored procedure can optionally have an output parameter that returns the number of rows updated/deleted (rows affected).</span></span> <span data-ttu-id="6c34e-144">必须使用 RowsAffectedParameter 方法配置此类参数。</span><span class="sxs-lookup"><span data-stu-id="6c34e-144">Such a parameter must be configured using the RowsAffectedParameter method.</span></span>  
<span data-ttu-id="6c34e-145">默认情况下，EF 使用 ExecuteNonQuery 的返回值来确定受影响的行数。</span><span class="sxs-lookup"><span data-stu-id="6c34e-145">By default EF uses the return value from ExecuteNonQuery to determine how many rows were affected.</span></span> <span data-ttu-id="6c34e-146">如果在过程中执行的任何逻辑会导致在执行结束时 ExecuteNonQuery 的返回值不正确（从 EF 的角度），则指定 rows 受影响的输出参数将很有用。</span><span class="sxs-lookup"><span data-stu-id="6c34e-146">Specifying a rows affected output parameter is useful if you perform any logic in your sproc that would result in the return value of ExecuteNonQuery being incorrect (from EF's perspective) at the end of execution.</span></span>  
- <span data-ttu-id="6c34e-147">对于每个并发标记，都将有一个名为 **\<property_name\>_Original**的参数（例如 Timestamp_Original）。</span><span class="sxs-lookup"><span data-stu-id="6c34e-147">For each concurrency token there will be a parameter named **\<property_name\>_Original** (for example, Timestamp_Original ).</span></span> <span data-ttu-id="6c34e-148">这会传递到此属性的原始值–从数据库查询时的值。</span><span class="sxs-lookup"><span data-stu-id="6c34e-148">This will be passed the original value of this property – the value when queried from the database.</span></span>  
    - <span data-ttu-id="6c34e-149">数据库计算的并发性标记（如时间戳）将只有原始值参数。</span><span class="sxs-lookup"><span data-stu-id="6c34e-149">Concurrency tokens that are computed by the database – such as timestamps – will only have an original value parameter.</span></span>  
    - <span data-ttu-id="6c34e-150">设置为并发令牌的非计算属性也会在更新过程中为新值提供一个参数。</span><span class="sxs-lookup"><span data-stu-id="6c34e-150">Non-computed properties that are set as concurrency tokens will also have a parameter for the new value in the update procedure.</span></span> <span data-ttu-id="6c34e-151">这会对新值使用已讨论的命名约定。</span><span class="sxs-lookup"><span data-stu-id="6c34e-151">This uses the naming conventions already discussed for new values.</span></span> <span data-ttu-id="6c34e-152">此类标记的一个示例将使用博客的 URL 作为并发标记，新值是必需的，因为你的代码可以将此值更新为新值（与仅由数据库更新的时间戳标记不同）。</span><span class="sxs-lookup"><span data-stu-id="6c34e-152">An example of such a token would be using a Blog's URL as a concurrency token, the new value is required because this can be updated to a new value by your code (unlike a Timestamp token which is only updated by the database).</span></span>  

<span data-ttu-id="6c34e-153">这是一个示例类，并用时间戳并发标记更新存储过程。</span><span class="sxs-lookup"><span data-stu-id="6c34e-153">This is an example class and update stored procedure with a timestamp concurrency token.</span></span>  

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

<span data-ttu-id="6c34e-154">下面是一个示例类，并使用非计算并发标记更新存储过程。</span><span class="sxs-lookup"><span data-stu-id="6c34e-154">Here is an example class and update stored procedure with non-computed concurrency token.</span></span>  

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

### <a name="overriding-the-defaults"></a><span data-ttu-id="6c34e-155">重写默认值</span><span class="sxs-lookup"><span data-stu-id="6c34e-155">Overriding the Defaults</span></span>  

<span data-ttu-id="6c34e-156">您可以选择引入受影响的行参数。</span><span class="sxs-lookup"><span data-stu-id="6c34e-156">You can optionally introduce a rows affected parameter.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.RowsAffectedParameter("rows_affected")));
```  

<span data-ttu-id="6c34e-157">对于数据库计算的并发标记（仅传递原始值），只需使用标准参数重命名机制来重命名原始值的参数。</span><span class="sxs-lookup"><span data-stu-id="6c34e-157">For database computed concurrency tokens – where only the original value is passed – you can just use the standard parameter renaming mechanism to rename the parameter for the original value.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.Timestamp, "blog_timestamp")));
```  

<span data-ttu-id="6c34e-158">对于非计算并发标记（同时传递原始值和新值），可以使用参数的重载，以便为每个参数提供名称。</span><span class="sxs-lookup"><span data-stu-id="6c34e-158">For non-computed concurrency tokens – where both the original and new value are passed – you can use an overload of Parameter that allows you to supply a name for each parameter.</span></span>  

``` csharp
modelBuilder
 .Entity<Blog>()
 .MapToStoredProcedures(s => s.Update(u => u.Parameter(b => b.Url, "blog_url", "blog_original_url")));
```  

## <a name="many-to-many-relationships"></a><span data-ttu-id="6c34e-159">多对多关系</span><span class="sxs-lookup"><span data-stu-id="6c34e-159">Many to Many Relationships</span></span>  

<span data-ttu-id="6c34e-160">我们将使用以下类作为此部分中的示例。</span><span class="sxs-lookup"><span data-stu-id="6c34e-160">We’ll use the following classes as an example in this section.</span></span>  

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

<span data-ttu-id="6c34e-161">多对多关系可以映射到具有以下语法的存储过程。</span><span class="sxs-lookup"><span data-stu-id="6c34e-161">Many to many relationships can be mapped to stored procedures with the following syntax.</span></span>  

``` csharp
modelBuilder  
  .Entity<Post>()  
  .HasMany(p => p.Tags)  
  .WithMany(t => t.Posts)  
  .MapToStoredProcedures();
```  

<span data-ttu-id="6c34e-162">如果未提供其他配置，则默认情况下使用以下存储过程形状。</span><span class="sxs-lookup"><span data-stu-id="6c34e-162">If no other configuration is supplied then the following stored procedure shape is used by default.</span></span>  

- <span data-ttu-id="6c34e-163">两个名为 **\<type_one\>\<type_two**\>_Insert\<**type_one\>** \<type_two\>_Delete PostTag_Insert PostTag_Delete 和的存储过程。</span><span class="sxs-lookup"><span data-stu-id="6c34e-163">Two stored procedures named **\<type_one\>\<type_two\>_Insert** and **\<type_one\>\<type_two\>_Delete** (for example, PostTag_Insert and PostTag_Delete).</span></span>  
- <span data-ttu-id="6c34e-164">参数将是每个类型的键值。</span><span class="sxs-lookup"><span data-stu-id="6c34e-164">The parameters will be the key value(s) for each type.</span></span> <span data-ttu-id="6c34e-165">要\<的每个参数的名称**type_name\>_\<property_name**\>（例如 Post_PostId 和 Tag_TagId）。</span><span class="sxs-lookup"><span data-stu-id="6c34e-165">The name of each parameter being **\<type_name\>_\<property_name\>** (for example, Post_PostId and Tag_TagId).</span></span>

<span data-ttu-id="6c34e-166">下面是 insert 和 update 存储过程的示例。</span><span class="sxs-lookup"><span data-stu-id="6c34e-166">Here are example insert and update stored procedures.</span></span>  

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

### <a name="overriding-the-defaults"></a><span data-ttu-id="6c34e-167">重写默认值</span><span class="sxs-lookup"><span data-stu-id="6c34e-167">Overriding the Defaults</span></span>  

<span data-ttu-id="6c34e-168">可以采用类似的方式配置过程和参数名称以与实体存储过程类似。</span><span class="sxs-lookup"><span data-stu-id="6c34e-168">The procedure and parameter names can be configured in a similar way to entity stored procedures.</span></span>  

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
