---
title: 验证-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 77d6a095-c0d0-471e-80b9-8f9aea6108b2
ms.openlocfilehash: 98d7bd08d841ee400afb62e1079f1a965f65e139
ms.sourcegitcommit: b4a5ed177b86bf7f81602106dab6b4acc18dfc18
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2019
ms.locfileid: "54316642"
---
# <a name="data-validation"></a><span data-ttu-id="8d5da-102">数据验证</span><span class="sxs-lookup"><span data-stu-id="8d5da-102">Data Validation</span></span>
> [!NOTE]
> <span data-ttu-id="8d5da-103">**EF4.1 及更高版本仅**的功能，Api，Entity Framework 4.1 中引入了此页所述的等。</span><span class="sxs-lookup"><span data-stu-id="8d5da-103">**EF4.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 4.1.</span></span> <span data-ttu-id="8d5da-104">如果您使用的是早期版本，部分或全部信息不适用</span><span class="sxs-lookup"><span data-stu-id="8d5da-104">If you are using an earlier version, some or all of the information does not apply</span></span>

<span data-ttu-id="8d5da-105">此页上的内容是从作者： Julie Lerman 最初编写一篇文章 ([http://thedatafarm.com](http://thedatafarm.com))。</span><span class="sxs-lookup"><span data-stu-id="8d5da-105">The content on this page is adapted from an article originally written by Julie Lerman ([http://thedatafarm.com](http://thedatafarm.com)).</span></span>

<span data-ttu-id="8d5da-106">实体框架提供了多种源通过客户端验证的用户界面或用于服务器端验证的验证功能。</span><span class="sxs-lookup"><span data-stu-id="8d5da-106">Entity Framework provides a great variety of validation features that can feed through to a user interface for client-side validation or be used for server-side validation.</span></span> <span data-ttu-id="8d5da-107">当使用 code first，可以指定使用注释或 fluent API 配置的验证。</span><span class="sxs-lookup"><span data-stu-id="8d5da-107">When using code first, you can specify validations using annotation or fluent API configurations.</span></span> <span data-ttu-id="8d5da-108">其他验证，以及更多复杂，可以在代码中指定和您的模型拥有从代码前，是否将工作模型优先或数据库优先。</span><span class="sxs-lookup"><span data-stu-id="8d5da-108">Additional validations, and more complex, can be specified in code and will work whether your model hails from code first, model first or database first.</span></span>

## <a name="the-model"></a><span data-ttu-id="8d5da-109">模型</span><span class="sxs-lookup"><span data-stu-id="8d5da-109">The model</span></span>

<span data-ttu-id="8d5da-110">我将演示如何使用类的一个简单的对验证：博客和文章。</span><span class="sxs-lookup"><span data-stu-id="8d5da-110">I’ll demonstrate the validations with a simple pair of classes: Blog and Post.</span></span>

``` csharp
    public class Blog
      {
          public int Id { get; set; }
          public string Title { get; set; }
          public string BloggerName { get; set; }
          public DateTime DateCreated { get; set; }
          public virtual ICollection<Post> Posts { get; set; }
          }
      }

      public class Post
      {
          public int Id { get; set; }
          public string Title { get; set; }
          public DateTime DateCreated { get; set; }
          public string Content { get; set; }
          public int BlogId { get; set; }
          public ICollection<Comment> Comments { get; set; }
      }
```

## <a name="data-annotations"></a><span data-ttu-id="8d5da-111">数据注释</span><span class="sxs-lookup"><span data-stu-id="8d5da-111">Data Annotations</span></span>

<span data-ttu-id="8d5da-112">代码首先使用 System.ComponentModel.DataAnnotations 程序集中的批注作为一种配置代码第一类。</span><span class="sxs-lookup"><span data-stu-id="8d5da-112">Code First uses annotations from the System.ComponentModel.DataAnnotations assembly as one means of configuring code first classes.</span></span> <span data-ttu-id="8d5da-113">这些批注包括那些提供必需的 MaxLength 和 MinLength 等规则。</span><span class="sxs-lookup"><span data-stu-id="8d5da-113">Among these annotations are those which provide rules such as the Required, MaxLength and MinLength.</span></span> <span data-ttu-id="8d5da-114">大量的.NET 客户端应用程序也能识别这些批注，例如，ASP.NET MVC。</span><span class="sxs-lookup"><span data-stu-id="8d5da-114">A number of .NET client applications also recognize these annotations, for example, ASP.NET MVC.</span></span> <span data-ttu-id="8d5da-115">您可以获得这些批注与这两个客户端和服务器端验证。</span><span class="sxs-lookup"><span data-stu-id="8d5da-115">You can achieve both client side and server side validation with these annotations.</span></span> <span data-ttu-id="8d5da-116">例如，可以强制博客 Title 属性是必需的属性。</span><span class="sxs-lookup"><span data-stu-id="8d5da-116">For example, you can force the Blog Title property to be a required property.</span></span>

``` csharp
    [Required]
    public string Title { get; set; }
```

<span data-ttu-id="8d5da-117">与任何其他代码或应用程序中的标记更改时，现有的 MVC 应用程序将执行客户端验证，甚至动态生成使用的属性和批注名称的消息。</span><span class="sxs-lookup"><span data-stu-id="8d5da-117">With no additional code or markup changes in the application, an existing MVC application will perform client side validation, even dynamically building a message using the property and annotation names.</span></span>

![图 1](~/ef6/media/figure01.png)

<span data-ttu-id="8d5da-119">文中重新创建此视图的方法，实体框架用于将新博客保存到数据库，但 MVC 的客户端验证会触发之前应用程序到达该代码。</span><span class="sxs-lookup"><span data-stu-id="8d5da-119">In the post back method of this Create view, Entity Framework is used to save the new blog to the database, but MVC’s client-side validation is triggered before the application reaches that code.</span></span>

<span data-ttu-id="8d5da-120">客户端验证不但是项目符号经得起考验。</span><span class="sxs-lookup"><span data-stu-id="8d5da-120">Client side validation is not bullet-proof however.</span></span> <span data-ttu-id="8d5da-121">用户可能会影响其浏览器的功能或更糟糕的是，黑客可能会使用一些技巧来避免 UI 验证。</span><span class="sxs-lookup"><span data-stu-id="8d5da-121">Users can impact features of their browser or worse yet, a hacker might use some trickery to avoid the UI validations.</span></span> <span data-ttu-id="8d5da-122">但是，实体框架还将识别所需的批注，并对其进行验证。</span><span class="sxs-lookup"><span data-stu-id="8d5da-122">But Entity Framework will also recognize the Required annotation and validate it.</span></span>

<span data-ttu-id="8d5da-123">对此进行测试的简单方法是禁用 MVC 的客户端验证功能。</span><span class="sxs-lookup"><span data-stu-id="8d5da-123">A simple way to test this is to disable MVC’s client-side validation feature.</span></span> <span data-ttu-id="8d5da-124">在 MVC 应用程序的 web.config 文件中，可以执行此操作。</span><span class="sxs-lookup"><span data-stu-id="8d5da-124">You can do this in the MVC application’s web.config file.</span></span> <span data-ttu-id="8d5da-125">AppSettings 节的 ClientValidationEnabled 有一个键。</span><span class="sxs-lookup"><span data-stu-id="8d5da-125">The appSettings section has a key for ClientValidationEnabled.</span></span> <span data-ttu-id="8d5da-126">将此项设置为 false 会阻止 UI 执行验证。</span><span class="sxs-lookup"><span data-stu-id="8d5da-126">Setting this key to false will prevent the UI from performing validations.</span></span>

``` xml
    <appSettings>
        <add key="ClientValidationEnabled"value="false"/>
        ...
    </appSettings>
```

<span data-ttu-id="8d5da-127">即使与禁用客户端验证，将在应用程序中获取相同的响应。</span><span class="sxs-lookup"><span data-stu-id="8d5da-127">Even with the client-side validation disabled, you will get the same response in your application.</span></span> <span data-ttu-id="8d5da-128">错误消息"标题字段是必填"将显示为。</span><span class="sxs-lookup"><span data-stu-id="8d5da-128">The error message “The Title field is required” will be displayed as.</span></span> <span data-ttu-id="8d5da-129">不过，现在它将是服务器端验证的结果。</span><span class="sxs-lookup"><span data-stu-id="8d5da-129">Except now it will be a result of server-side validation.</span></span> <span data-ttu-id="8d5da-130">实体框架将前进行验证所需的注释上 （甚至厌烦与生成和要发送到数据库的 INSERT 命令） 并将错误返回给 MVC 将显示消息。</span><span class="sxs-lookup"><span data-stu-id="8d5da-130">Entity Framework will perform the validation on the Required annotation (before it even bothers to build and INSERT command to send to the database) and return the error to MVC which will display the message.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="8d5da-131">Fluent API</span><span class="sxs-lookup"><span data-stu-id="8d5da-131">Fluent API</span></span>

<span data-ttu-id="8d5da-132">您可以使用代码第一的 fluent API，而不是批注以获取同一个客户端端和服务器端验证。</span><span class="sxs-lookup"><span data-stu-id="8d5da-132">You can use code first’s fluent API instead of annotations to get the same client side & server side validation.</span></span> <span data-ttu-id="8d5da-133">而不是使用必需的我将介绍此过程通过 MaxLength 验证。</span><span class="sxs-lookup"><span data-stu-id="8d5da-133">Rather than use Required, I’ll show you this using a MaxLength validation.</span></span>

<span data-ttu-id="8d5da-134">如代码首先构建从类模型应用 Fluent API 配置。</span><span class="sxs-lookup"><span data-stu-id="8d5da-134">Fluent API configurations are applied as code first is building the model from the classes.</span></span> <span data-ttu-id="8d5da-135">可以通过重写 DbContext 类的 OnModelCreating 方法注入配置。</span><span class="sxs-lookup"><span data-stu-id="8d5da-135">You can inject the configurations by overriding the DbContext class’ OnModelCreating  method.</span></span> <span data-ttu-id="8d5da-136">下面是配置指定 BloggerName 属性可以长度不得超过 10 个字符。</span><span class="sxs-lookup"><span data-stu-id="8d5da-136">Here is a configuration specifying that the BloggerName property can be no longer than 10 characters.</span></span>

``` csharp
    public class BlogContext : DbContext
      {
          public DbSet<Blog> Blogs { get; set; }
          public DbSet<Post> Posts { get; set; }
          public DbSet<Comment> Comments { get; set; }

          protected override void OnModelCreating(DbModelBuilder modelBuilder)
          {
              modelBuilder.Entity<Blog>().Property(p => p.BloggerName).HasMaxLength(10);
          }
        }
```

<span data-ttu-id="8d5da-137">引发验证错误基于 Fluent API 配置将不自动 reach UI，但您可以捕获其代码，然后响应中相应地。</span><span class="sxs-lookup"><span data-stu-id="8d5da-137">Validation errors thrown based on the Fluent API configurations will not automatically reach the UI, but you can capture it in code and then respond to it accordingly.</span></span>

<span data-ttu-id="8d5da-138">下面的一些异常，处理应用程序的 BlogController 类，它捕获该验证错误，当尝试使用超过 10 个字符最大 BloggerName 保存一个博客，实体框架中的错误代码。</span><span class="sxs-lookup"><span data-stu-id="8d5da-138">Here’s some exception handling error code in the application’s BlogController class that captures that validation error when Entity Framework attempts to save a blog with a BloggerName that exceeds the 10 character maximum.</span></span>

``` csharp
    [HttpPost]
    public ActionResult Edit(int id, Blog blog)
    {
        try
        {
            db.Entry(blog).State = EntityState.Modified;
            db.SaveChanges();
            return RedirectToAction("Index");
        }
        catch(DbEntityValidationException ex)
        {
            var error = ex.EntityValidationErrors.First().ValidationErrors.First();
            this.ModelState.AddModelError(error.PropertyName, error.ErrorMessage);
            return View();
        }
    }
```

<span data-ttu-id="8d5da-139">验证不会自动传递回这正是使用 ModelState.AddModelError 的附加代码正在使用的视图。</span><span class="sxs-lookup"><span data-stu-id="8d5da-139">The validation doesn’t automatically get passed back into the view which is why the additional code that uses ModelState.AddModelError is being used.</span></span> <span data-ttu-id="8d5da-140">这可确保错误详细信息视图将随后使用 ValidationMessageFor Htmlhelper 显示错误使它。</span><span class="sxs-lookup"><span data-stu-id="8d5da-140">This ensures that the error details make it to the view which will then use the ValidationMessageFor Htmlhelper to display the error.</span></span>

``` csharp
    @Html.ValidationMessageFor(model => model.BloggerName)
```

## <a name="ivalidatableobject"></a><span data-ttu-id="8d5da-141">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="8d5da-141">IValidatableObject</span></span>

<span data-ttu-id="8d5da-142">IValidatableObject 是位于 System.ComponentModel.DataAnnotations 的接口。</span><span class="sxs-lookup"><span data-stu-id="8d5da-142">IValidatableObject is an interface that lives in System.ComponentModel.DataAnnotations.</span></span> <span data-ttu-id="8d5da-143">尽管这不是 Entity Framework API 的一部分，你仍然可以利用它进行服务器端验证在实体框架的类中。</span><span class="sxs-lookup"><span data-stu-id="8d5da-143">While it is not part of the Entity Framework API, you can still leverage it for server-side validation in your Entity Framework classes.</span></span> <span data-ttu-id="8d5da-144">IValidatableObject 提供实体框架将调用期间 SaveChanges 或你的验证方法可以调用自己想要验证的类的任何时间。</span><span class="sxs-lookup"><span data-stu-id="8d5da-144">IValidatableObject provides a Validate method that Entity Framework will call during SaveChanges or you can call yourself any time you want to validate the classes.</span></span>

<span data-ttu-id="8d5da-145">如所需的配置和 MaxLength 单个字段执行验证。</span><span class="sxs-lookup"><span data-stu-id="8d5da-145">Configurations such as Required and MaxLength perform validaton on a single field.</span></span> <span data-ttu-id="8d5da-146">在验证方法可以具有变得更加复杂的逻辑，例如，比较两个字段。</span><span class="sxs-lookup"><span data-stu-id="8d5da-146">In the Validate method you can have even more complex logic, for example, comparing two fields.</span></span>

<span data-ttu-id="8d5da-147">在下面的示例，博客类已扩展，实现 IValidatableObject，然后提供标题和 BloggerName 无法匹配的规则。</span><span class="sxs-lookup"><span data-stu-id="8d5da-147">In the following example, the Blog class has been extended to implement IValidatableObject and then provide a rule that the Title and BloggerName cannot match.</span></span>

``` csharp
    public class Blog : IValidatableObject
     {
         public int Id { get; set; }
         [Required]
         public string Title { get; set; }
         public string BloggerName { get; set; }
         public DateTime DateCreated { get; set; }
         public virtual ICollection<Post> Posts { get; set; }

         public IEnumerable<ValidationResult> Validate(ValidationContext validationContext)
         {
             if (Title == BloggerName)
             {
                 yield return new ValidationResult
                  ("Blog Title cannot match Blogger Name", new[] { "Title", “BloggerName” });
             }
         }
     }
```

<span data-ttu-id="8d5da-148">ValidationResult 构造函数采用一个字符串，表示错误消息和一个表示与验证相关联的成员名称的字符串数组。</span><span class="sxs-lookup"><span data-stu-id="8d5da-148">The ValidationResult constructor takes a string that represents the error message and an array of strings that represent the member names that are associated with the validation.</span></span> <span data-ttu-id="8d5da-149">由于此验证检查的标题和 BloggerName，将返回这两个属性名称。</span><span class="sxs-lookup"><span data-stu-id="8d5da-149">Since this validation checks both the Title and the BloggerName, both property names are returned.</span></span>

<span data-ttu-id="8d5da-150">与 Fluent API 提供的验证，此验证结果会被视为由视图和我之前使用将错误添加到 ModelState 的异常处理程序是不必要的。</span><span class="sxs-lookup"><span data-stu-id="8d5da-150">Unlike the validation provided by the Fluent API, this validation result will be recognized by the View and the exception handler that I used earlier to add the error into ModelState is unnecessary.</span></span> <span data-ttu-id="8d5da-151">由于我在 ValidationResult 中设置两个属性名称，MVC HtmlHelpers 这些属性的两个显示的错误消息。</span><span class="sxs-lookup"><span data-stu-id="8d5da-151">Because I set both property names in the ValidationResult, the MVC HtmlHelpers display the error message for both of those properties.</span></span>

![图 2](~/ef6/media/figure02.png)

## <a name="dbcontextvalidateentity"></a><span data-ttu-id="8d5da-153">DbContext.ValidateEntity</span><span class="sxs-lookup"><span data-stu-id="8d5da-153">DbContext.ValidateEntity</span></span>

<span data-ttu-id="8d5da-154">DbContext 具有名为 ValidateEntity 的可重写方法。</span><span class="sxs-lookup"><span data-stu-id="8d5da-154">DbContext has an Overridable method called ValidateEntity.</span></span> <span data-ttu-id="8d5da-155">当调用 SaveChanges 时，实体框架将调用此方法，其状态不是未更改其缓存中每个实体。</span><span class="sxs-lookup"><span data-stu-id="8d5da-155">When you call SaveChanges, Entity Framework will call this method for each entity in its cache whose state is not Unchanged.</span></span> <span data-ttu-id="8d5da-156">可以直接在此处或甚至使用此方法调用，例如，在上一节中添加的 Blog.Validate 方法将验证逻辑。</span><span class="sxs-lookup"><span data-stu-id="8d5da-156">You can put validation logic directly in here or even use this method to call, for example, the Blog.Validate method added in the previous section.</span></span>

<span data-ttu-id="8d5da-157">下面是验证以确保文章标题未已使用的新帖子的 ValidateEntity 重写的示例。</span><span class="sxs-lookup"><span data-stu-id="8d5da-157">Here’s an example of a ValidateEntity override that validates new Posts to ensure that the post title hasn’t been used already.</span></span> <span data-ttu-id="8d5da-158">它首先检查以查看实体是否为 post，并添加其状态。</span><span class="sxs-lookup"><span data-stu-id="8d5da-158">It first checks to see if the entity is a post and that its state is Added.</span></span> <span data-ttu-id="8d5da-159">如果是这样，然后它会查找数据库以查看是否已存在具有相同标题的帖子中。</span><span class="sxs-lookup"><span data-stu-id="8d5da-159">If that’s the case, then it looks in the database to see if there is already a post with the same title.</span></span> <span data-ttu-id="8d5da-160">如果已存在现有的帖子，则会创建新 DbEntityValidationResult。</span><span class="sxs-lookup"><span data-stu-id="8d5da-160">If there is an existing post already, then a new DbEntityValidationResult is created.</span></span>

<span data-ttu-id="8d5da-161">DbEntityValidationResult 存储 DbEntityEntry 和针对单个实体 DbValidationErrors ICollection。</span><span class="sxs-lookup"><span data-stu-id="8d5da-161">DbEntityValidationResult houses a DbEntityEntry and an ICollection of DbValidationErrors for a single entity.</span></span> <span data-ttu-id="8d5da-162">在此方法开始时，实例化 DbEntityValidationResult，然后发现任何错误将被添加到其 ValidationErrors 集合。</span><span class="sxs-lookup"><span data-stu-id="8d5da-162">At the start of this method, a DbEntityValidationResult is instantiated and then any errors that are discovered are added into its ValidationErrors collection.</span></span>

``` csharp
    protected override DbEntityValidationResult ValidateEntity (
        System.Data.Entity.Infrastructure.DbEntityEntry entityEntry,
        IDictionary\<object, object> items)
    {
        var result = new DbEntityValidationResult(entityEntry, new List<DbValidationError>());
        if (entityEntry.Entity is Post && entityEntry.State == EntityState.Added)
        {
            Post post = entityEntry.Entity as Post;
            //check for uniqueness of post title
            if (Posts.Where(p => p.Title == post.Title).Count() > 0)
            {
                result.ValidationErrors.Add(
                        new System.Data.Entity.Validation.DbValidationError("Title",
                        "Post title must be unique."));
            }
        }

        if (result.ValidationErrors.Count > 0)
        {
            return result;
        }
        else
        {
         return base.ValidateEntity(entityEntry, items);
        }
    }
```

## <a name="explicitly-triggering-validation"></a><span data-ttu-id="8d5da-163">显式触发验证</span><span class="sxs-lookup"><span data-stu-id="8d5da-163">Explicitly triggering validation</span></span>

<span data-ttu-id="8d5da-164">调用 SaveChanges 触发所有本文中所述的验证。</span><span class="sxs-lookup"><span data-stu-id="8d5da-164">A call to SaveChanges triggers all of the validations covered in this article.</span></span> <span data-ttu-id="8d5da-165">但无需依赖于 SaveChanges。</span><span class="sxs-lookup"><span data-stu-id="8d5da-165">But you don’t need to rely on SaveChanges.</span></span> <span data-ttu-id="8d5da-166">您可能想要验证应用程序中的其他位置。</span><span class="sxs-lookup"><span data-stu-id="8d5da-166">You may prefer to validate elsewhere in your application.</span></span>

<span data-ttu-id="8d5da-167">DbContext.GetValidationErrors 将触发的所有验证、 通过注释或 Fluent API 定义的、 在 IValidatableObject (例如，Blog.Validate) 中创建的验证和 DbContext.ValidateEntity 中执行的验证方法。</span><span class="sxs-lookup"><span data-stu-id="8d5da-167">DbContext.GetValidationErrors will trigger all of the validations, those defined by annotations or the Fluent API, the validation created in IValidatableObject (for example, Blog.Validate), and the validations performed in the DbContext.ValidateEntity method.</span></span>

<span data-ttu-id="8d5da-168">下面的代码将调用 GetValidationErrors DbContext 的当前实例上。</span><span class="sxs-lookup"><span data-stu-id="8d5da-168">The following code will call GetValidationErrors on the current instance of a DbContext.</span></span> <span data-ttu-id="8d5da-169">ValidationErrors 按实体类型到 DbValidationResults 分组。</span><span class="sxs-lookup"><span data-stu-id="8d5da-169">ValidationErrors are grouped by entity type into DbValidationResults.</span></span> <span data-ttu-id="8d5da-170">此代码首先循环通过该方法返回 DbValidationResults，然后通过在每个 ValidationError。</span><span class="sxs-lookup"><span data-stu-id="8d5da-170">The code iterates first through the DbValidationResults returned by the method and then through each ValidationError inside.</span></span>

``` csharp
    foreach (var validationResults in db.GetValidationErrors())
        {
            foreach (var error in validationResults.ValidationErrors)
            {
                Debug.WriteLine(
                                  "Entity Property: {0}, Error {1}",
                                  error.PropertyName,
                                  error.ErrorMessage);
            }
        }
```

## <a name="other-considerations-when-using-validation"></a><span data-ttu-id="8d5da-171">使用验证时的其他注意事项</span><span class="sxs-lookup"><span data-stu-id="8d5da-171">Other considerations when using validation</span></span>

<span data-ttu-id="8d5da-172">下面是几个使用实体框架验证时要考虑其他要点：</span><span class="sxs-lookup"><span data-stu-id="8d5da-172">Here are a few other points to consider when using Entity Framework validation:</span></span>

-   <span data-ttu-id="8d5da-173">在验证过程中禁用延迟加载。</span><span class="sxs-lookup"><span data-stu-id="8d5da-173">Lazy loading is disabled during validation.</span></span>
-   <span data-ttu-id="8d5da-174">EF 将验证数据批注在非映射属性 （未映射到数据库中的列的属性）。</span><span class="sxs-lookup"><span data-stu-id="8d5da-174">EF will validate data annotations on non-mapped properties (properties that are not mapped to a column in the database).</span></span>
-   <span data-ttu-id="8d5da-175">更改在 SaveChanges 期间检测到的后执行验证。</span><span class="sxs-lookup"><span data-stu-id="8d5da-175">Validation is performed after changes are detected during SaveChanges.</span></span> <span data-ttu-id="8d5da-176">如果在验证过程进行了更改它由你负责通知更改跟踪器。</span><span class="sxs-lookup"><span data-stu-id="8d5da-176">If you make changes during validation it is your responsibility to notify the change tracker.</span></span>
-   <span data-ttu-id="8d5da-177">如果在验证过程中发生错误，将引发 DbUnexpectedValidationException。</span><span class="sxs-lookup"><span data-stu-id="8d5da-177">DbUnexpectedValidationException is thrown if errors occur during validation.</span></span>
-   <span data-ttu-id="8d5da-178">实体框架包括模型 （所需的最大长度等） 中的分面时会进行验证，即使不是您的类上的数据批注和/或使用 EF 设计器来创建模型。</span><span class="sxs-lookup"><span data-stu-id="8d5da-178">Facets that Entity Framework includes in the model (maximum length, required, etc.) will cause validation, even if there are not data annotations on your classes and/or you used the EF Designer to create your model.</span></span>
-   <span data-ttu-id="8d5da-179">优先顺序规则：</span><span class="sxs-lookup"><span data-stu-id="8d5da-179">Precedence rules:</span></span>
    -   <span data-ttu-id="8d5da-180">Fluent API 调用重写相应的数据注释</span><span class="sxs-lookup"><span data-stu-id="8d5da-180">Fluent API calls override the corresponding data annotations</span></span>
-   <span data-ttu-id="8d5da-181">执行顺序：</span><span class="sxs-lookup"><span data-stu-id="8d5da-181">Execution order:</span></span>
    -   <span data-ttu-id="8d5da-182">属性验证之前类型验证</span><span class="sxs-lookup"><span data-stu-id="8d5da-182">Property validation occurs before type validation</span></span>
    -   <span data-ttu-id="8d5da-183">类型验证时，才出现属性验证成功</span><span class="sxs-lookup"><span data-stu-id="8d5da-183">Type validation only occurs if property validation succeeds</span></span>
-   <span data-ttu-id="8d5da-184">如果属性是复杂还将包括其验证：</span><span class="sxs-lookup"><span data-stu-id="8d5da-184">If a property is complex its validation will also include:</span></span>
    -   <span data-ttu-id="8d5da-185">属性级别验证的复杂类型属性</span><span class="sxs-lookup"><span data-stu-id="8d5da-185">Property-level validation on the complex type properties</span></span>
    -   <span data-ttu-id="8d5da-186">在复杂类型，包括复杂类型上的 IValidatableObject 验证类型级别的验证</span><span class="sxs-lookup"><span data-stu-id="8d5da-186">Type level validation on the complex type, including IValidatableObject validation on the complex type</span></span>

## <a name="summary"></a><span data-ttu-id="8d5da-187">总结</span><span class="sxs-lookup"><span data-stu-id="8d5da-187">Summary</span></span>

<span data-ttu-id="8d5da-188">在实体框架验证 API 非常非常适合使用 MVC 中的客户端验证，但无需依赖于客户端验证。</span><span class="sxs-lookup"><span data-stu-id="8d5da-188">The validation API in Entity Framework plays very nicely with client side validation in MVC but you don't have to rely on client-side validation.</span></span> <span data-ttu-id="8d5da-189">实体框架将负责在服务器端验证 DataAnnotations 或已使用 code first Fluent API 应用的配置。</span><span class="sxs-lookup"><span data-stu-id="8d5da-189">Entity Framework will take care of the validation on the server side for DataAnnotations or configurations you've applied with the code first Fluent API.</span></span>

<span data-ttu-id="8d5da-190">您还了解到大量的自定义行为，是否使用 IValidatableObject 接口或利用 DbContet.ValidateEntity 方法的扩展点。</span><span class="sxs-lookup"><span data-stu-id="8d5da-190">You also saw a number of extensibility points for customizing the behavior whether you use the IValidatableObject interface or tap into the DbContet.ValidateEntity method.</span></span> <span data-ttu-id="8d5da-191">并验证这些最后两个方法可通过 DbContext，无论使用 Code First、 Model First 或 Database First 工作流来描述概念模型。</span><span class="sxs-lookup"><span data-stu-id="8d5da-191">And these last two means of validation are available through the DbContext, whether you use the Code First, Model First or Database First workflow to describe your conceptual model.</span></span>
