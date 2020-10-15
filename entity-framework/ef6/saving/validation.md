---
title: 验证-EF6
description: 实体框架6中的验证
author: ajcvickers
ms.date: 10/23/2016
ms.openlocfilehash: 224cdd8b033b0ea534efb547fd4d39ac922a6faa
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064401"
---
# <a name="data-validation"></a><span data-ttu-id="a87b1-103">数据验证</span><span class="sxs-lookup"><span data-stu-id="a87b1-103">Data Validation</span></span>
> [!NOTE]
> <span data-ttu-id="a87b1-104">**仅限 ef 4.1** -在实体框架4.1 中引入了本页中所述的功能、api 等。</span><span class="sxs-lookup"><span data-stu-id="a87b1-104">**EF4.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 4.1.</span></span> <span data-ttu-id="a87b1-105">如果使用的是早期版本，则不会应用部分或全部信息</span><span class="sxs-lookup"><span data-stu-id="a87b1-105">If you are using an earlier version, some or all of the information does not apply</span></span>

<span data-ttu-id="a87b1-106">此页面上的内容适用于最初由 Julie Lerman () 编写的文章 [https://thedatafarm.com](https://thedatafarm.com) 。</span><span class="sxs-lookup"><span data-stu-id="a87b1-106">The content on this page is adapted from an article originally written by Julie Lerman ([https://thedatafarm.com](https://thedatafarm.com)).</span></span>

<span data-ttu-id="a87b1-107">实体框架提供了一种非常丰富的验证功能，这些功能可用于向用户界面提供客户端验证或用于服务器端验证。</span><span class="sxs-lookup"><span data-stu-id="a87b1-107">Entity Framework provides a great variety of validation features that can feed through to a user interface for client-side validation or be used for server-side validation.</span></span> <span data-ttu-id="a87b1-108">使用 code first 时，可以使用批注或 Fluent API 配置来指定验证。</span><span class="sxs-lookup"><span data-stu-id="a87b1-108">When using code first, you can specify validations using annotation or fluent API configurations.</span></span> <span data-ttu-id="a87b1-109">可以在代码中指定其他验证和更复杂的验证，无论模型是从代码 hails、模型优先还是数据库优先进行操作，都可以使用。</span><span class="sxs-lookup"><span data-stu-id="a87b1-109">Additional validations, and more complex, can be specified in code and will work whether your model hails from code first, model first or database first.</span></span>

## <a name="the-model"></a><span data-ttu-id="a87b1-110">模型</span><span class="sxs-lookup"><span data-stu-id="a87b1-110">The model</span></span>

<span data-ttu-id="a87b1-111">我将使用一对简单的类来演示验证：博客和文章。</span><span class="sxs-lookup"><span data-stu-id="a87b1-111">I'll demonstrate the validations with a simple pair of classes: Blog and Post.</span></span>

``` csharp
public class Blog
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string BloggerName { get; set; }
    public DateTime DateCreated { get; set; }
    public virtual ICollection<Post> Posts { get; set; }
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

## <a name="data-annotations"></a><span data-ttu-id="a87b1-112">数据注释</span><span class="sxs-lookup"><span data-stu-id="a87b1-112">Data Annotations</span></span>

<span data-ttu-id="a87b1-113">Code First 使用 `System.ComponentModel.DataAnnotations` 程序集中的批注作为配置代码优先类的一种方法。</span><span class="sxs-lookup"><span data-stu-id="a87b1-113">Code First uses annotations from the `System.ComponentModel.DataAnnotations` assembly as one means of configuring code first classes.</span></span> <span data-ttu-id="a87b1-114">在这些批注中，这两个批注都提供诸如 `Required` 、和等规则 `MaxLength` `MinLength` 。</span><span class="sxs-lookup"><span data-stu-id="a87b1-114">Among these annotations are those which provide rules such as the `Required`, `MaxLength` and `MinLength`.</span></span> <span data-ttu-id="a87b1-115">许多 .NET 客户端应用程序还识别这些批注，例如，ASP.NET MVC。</span><span class="sxs-lookup"><span data-stu-id="a87b1-115">A number of .NET client applications also recognize these annotations, for example, ASP.NET MVC.</span></span> <span data-ttu-id="a87b1-116">您可以通过这些批注实现客户端和服务器端验证。</span><span class="sxs-lookup"><span data-stu-id="a87b1-116">You can achieve both client side and server side validation with these annotations.</span></span> <span data-ttu-id="a87b1-117">例如，你可以强制 "博客 Title" 属性为必需属性。</span><span class="sxs-lookup"><span data-stu-id="a87b1-117">For example, you can force the Blog Title property to be a required property.</span></span>

``` csharp
[Required]
public string Title { get; set; }
```

<span data-ttu-id="a87b1-118">如果应用程序中没有额外的代码或标记更改，则现有 MVC 应用程序会执行客户端验证，甚至使用属性和批注名称动态生成消息。</span><span class="sxs-lookup"><span data-stu-id="a87b1-118">With no additional code or markup changes in the application, an existing MVC application will perform client side validation, even dynamically building a message using the property and annotation names.</span></span>

![图 1](~/ef6/media/figure01.png)

<span data-ttu-id="a87b1-120">在此 "创建" 视图的 "回发" 方法中，实体框架用于将新的博客保存到数据库中，但在应用程序到达该代码之前触发了 MVC 的客户端验证。</span><span class="sxs-lookup"><span data-stu-id="a87b1-120">In the post back method of this Create view, Entity Framework is used to save the new blog to the database, but MVC's client-side validation is triggered before the application reaches that code.</span></span>

<span data-ttu-id="a87b1-121">不过，客户端验证不是项目符号。</span><span class="sxs-lookup"><span data-stu-id="a87b1-121">Client side validation is not bullet-proof however.</span></span> <span data-ttu-id="a87b1-122">用户可能会影响浏览器的功能或更糟的功能，黑客可能会使用某些 trickery 来避免 UI 验证。</span><span class="sxs-lookup"><span data-stu-id="a87b1-122">Users can impact features of their browser or worse yet, a hacker might use some trickery to avoid the UI validations.</span></span> <span data-ttu-id="a87b1-123">但实体框架还将识别该 `Required` 注释并对其进行验证。</span><span class="sxs-lookup"><span data-stu-id="a87b1-123">But Entity Framework will also recognize the `Required` annotation and validate it.</span></span>

<span data-ttu-id="a87b1-124">对此进行测试的一种简单方法是禁用 MVC 的客户端验证功能。</span><span class="sxs-lookup"><span data-stu-id="a87b1-124">A simple way to test this is to disable MVC's client-side validation feature.</span></span> <span data-ttu-id="a87b1-125">可以在 MVC 应用程序的 web.config 文件中执行此操作。</span><span class="sxs-lookup"><span data-stu-id="a87b1-125">You can do this in the MVC application's web.config file.</span></span> <span data-ttu-id="a87b1-126">AppSettings 节具有 ClientValidationEnabled 的键。</span><span class="sxs-lookup"><span data-stu-id="a87b1-126">The appSettings section has a key for ClientValidationEnabled.</span></span> <span data-ttu-id="a87b1-127">将此项设置为 false 将阻止 UI 执行验证。</span><span class="sxs-lookup"><span data-stu-id="a87b1-127">Setting this key to false will prevent the UI from performing validations.</span></span>

``` xml
<appSettings>
    <add key="ClientValidationEnabled"value="false"/>
    ...
</appSettings>
```

<span data-ttu-id="a87b1-128">即使已禁用客户端验证，也会在应用程序中获得相同的响应。</span><span class="sxs-lookup"><span data-stu-id="a87b1-128">Even with the client-side validation disabled, you will get the same response in your application.</span></span> <span data-ttu-id="a87b1-129">错误消息 "需要标题字段" 将像以前一样显示。</span><span class="sxs-lookup"><span data-stu-id="a87b1-129">The error message "The Title field is required" will be displayed as before.</span></span> <span data-ttu-id="a87b1-130">除非现在是服务器端验证的结果。</span><span class="sxs-lookup"><span data-stu-id="a87b1-130">Except now it will be a result of server-side validation.</span></span> <span data-ttu-id="a87b1-131">实体框架将在批注 (上执行验证， `Required` 甚至麻烦生成 `INSERT` 发送到数据库的命令) 并将错误返回给将显示该消息的 MVC。</span><span class="sxs-lookup"><span data-stu-id="a87b1-131">Entity Framework will perform the validation on the `Required` annotation (before it even bothers to build an `INSERT` command to send to the database) and return the error to MVC which will display the message.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="a87b1-132">Fluent API</span><span class="sxs-lookup"><span data-stu-id="a87b1-132">Fluent API</span></span>

<span data-ttu-id="a87b1-133">您可以使用 code first 的 Fluent API 而不是使用批注来获取相同的客户端 & 服务器端验证。</span><span class="sxs-lookup"><span data-stu-id="a87b1-133">You can use code first's fluent API instead of annotations to get the same client side & server side validation.</span></span> <span data-ttu-id="a87b1-134">`Required`我将使用 MaxLength 验证向您展示这一点，而不是使用。</span><span class="sxs-lookup"><span data-stu-id="a87b1-134">Rather than use `Required`, I'll show you this using a MaxLength validation.</span></span>

<span data-ttu-id="a87b1-135">当代码优先从类生成模型时，将应用 "熟知 API 配置"。</span><span class="sxs-lookup"><span data-stu-id="a87b1-135">Fluent API configurations are applied as code first is building the model from the classes.</span></span> <span data-ttu-id="a87b1-136">可以通过重写 DbContext 类的 OnModelCreating 方法注入配置。</span><span class="sxs-lookup"><span data-stu-id="a87b1-136">You can inject the configurations by overriding the DbContext class' OnModelCreating  method.</span></span> <span data-ttu-id="a87b1-137">以下配置指定 BloggerName 属性的长度不能超过10个字符。</span><span class="sxs-lookup"><span data-stu-id="a87b1-137">Here is a configuration specifying that the BloggerName property can be no longer than 10 characters.</span></span>

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

<span data-ttu-id="a87b1-138">基于流畅 API 配置引发的验证错误不会自动到达 UI，但你可以在代码中捕获该错误，然后相应地对其进行响应。</span><span class="sxs-lookup"><span data-stu-id="a87b1-138">Validation errors thrown based on the Fluent API configurations will not automatically reach the UI, but you can capture it in code and then respond to it accordingly.</span></span>

<span data-ttu-id="a87b1-139">下面是应用程序的 BlogController 类中的一些异常处理错误代码，该代码在实体框架尝试使用超过10个字符的 BloggerName 保存博客时捕获验证错误。</span><span class="sxs-lookup"><span data-stu-id="a87b1-139">Here's some exception handling error code in the application's BlogController class that captures that validation error when Entity Framework attempts to save a blog with a BloggerName that exceeds the 10 character maximum.</span></span>

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
    catch (DbEntityValidationException ex)
    {
        var error = ex.EntityValidationErrors.First().ValidationErrors.First();
        this.ModelState.AddModelError(error.PropertyName, error.ErrorMessage);
        return View();
    }
}
```

<span data-ttu-id="a87b1-140">验证不会自动传递回视图，这就是使用使用的其他代码的原因 `ModelState.AddModelError` 。</span><span class="sxs-lookup"><span data-stu-id="a87b1-140">The validation doesn't automatically get passed back into the view which is why the additional code that uses `ModelState.AddModelError` is being used.</span></span> <span data-ttu-id="a87b1-141">这可以确保错误详细信息使其成为视图，然后使用 `ValidationMessageFor` Htmlhelper 显示错误。</span><span class="sxs-lookup"><span data-stu-id="a87b1-141">This ensures that the error details make it to the view which will then use the `ValidationMessageFor` Htmlhelper to display the error.</span></span>

``` csharp
@Html.ValidationMessageFor(model => model.BloggerName)
```

## <a name="ivalidatableobject"></a><span data-ttu-id="a87b1-142">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="a87b1-142">IValidatableObject</span></span>

<span data-ttu-id="a87b1-143">`IValidatableObject` 是驻留在中的接口 `System.ComponentModel.DataAnnotations` 。</span><span class="sxs-lookup"><span data-stu-id="a87b1-143">`IValidatableObject` is an interface that lives in `System.ComponentModel.DataAnnotations`.</span></span> <span data-ttu-id="a87b1-144">尽管它不是实体框架 API 的一部分，但仍可在实体框架类中利用它进行服务器端验证。</span><span class="sxs-lookup"><span data-stu-id="a87b1-144">While it is not part of the Entity Framework API, you can still leverage it for server-side validation in your Entity Framework classes.</span></span> <span data-ttu-id="a87b1-145">`IValidatableObject` 提供 `Validate` 实体框架将在 SaveChanges 期间调用的方法，也可以在任何时候想要验证这些类时调用。</span><span class="sxs-lookup"><span data-stu-id="a87b1-145">`IValidatableObject` provides a `Validate` method that Entity Framework will call during SaveChanges or you can call yourself any time you want to validate the classes.</span></span>

<span data-ttu-id="a87b1-146">和等配置 `Required` 对 `MaxLength` 单个字段执行验证。</span><span class="sxs-lookup"><span data-stu-id="a87b1-146">Configurations such as `Required` and `MaxLength` perform validation on a single field.</span></span> <span data-ttu-id="a87b1-147">在方法中， `Validate` 可以有更复杂的逻辑，例如，比较两个字段。</span><span class="sxs-lookup"><span data-stu-id="a87b1-147">In the `Validate` method you can have even more complex logic, for example, comparing two fields.</span></span>

<span data-ttu-id="a87b1-148">在下面的示例中， `Blog` 类已扩展为实现， `IValidatableObject` 然后提供 `Title` 和 `BloggerName` 不能匹配的规则。</span><span class="sxs-lookup"><span data-stu-id="a87b1-148">In the following example, the `Blog` class has been extended to implement `IValidatableObject` and then provide a rule that the `Title` and `BloggerName` cannot match.</span></span>

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
            yield return new ValidationResult(
                "Blog Title cannot match Blogger Name",
                new[] { nameof(Title), nameof(BloggerName) });
        }
    }
}
```

<span data-ttu-id="a87b1-149">`ValidationResult`构造函数采用一个 `string` 表示错误消息的，以及一个表示 `string` 与验证关联的成员名称的数组。</span><span class="sxs-lookup"><span data-stu-id="a87b1-149">The `ValidationResult` constructor takes a `string` that represents the error message and an array of `string`s that represent the member names that are associated with the validation.</span></span> <span data-ttu-id="a87b1-150">由于此验证同时检查 `Title` 和 `BloggerName` ，同时返回这两个属性名。</span><span class="sxs-lookup"><span data-stu-id="a87b1-150">Since this validation checks both the `Title` and the `BloggerName`, both property names are returned.</span></span>

<span data-ttu-id="a87b1-151">与 "流畅 API" 提供的验证不同，此验证结果将被视图识别，并且之前用于将错误添加到的异常处理程序不 `ModelState` 是必需的。</span><span class="sxs-lookup"><span data-stu-id="a87b1-151">Unlike the validation provided by the Fluent API, this validation result will be recognized by the View and the exception handler that I used earlier to add the error into `ModelState` is unnecessary.</span></span> <span data-ttu-id="a87b1-152">因为我在中设置了这两个属性名称 `ValidationResult` ，所以 MVC HtmlHelpers 将显示这两个属性的错误消息。</span><span class="sxs-lookup"><span data-stu-id="a87b1-152">Because I set both property names in the `ValidationResult`, the MVC HtmlHelpers display the error message for both of those properties.</span></span>

![图 2](~/ef6/media/figure02.png)

## <a name="dbcontextvalidateentity"></a><span data-ttu-id="a87b1-154">DbContext. ValidateEntity</span><span class="sxs-lookup"><span data-stu-id="a87b1-154">DbContext.ValidateEntity</span></span>

<span data-ttu-id="a87b1-155">`DbContext` 具有一个名为的可重写方法 `ValidateEntity` 。</span><span class="sxs-lookup"><span data-stu-id="a87b1-155">`DbContext` has an overridable method called `ValidateEntity`.</span></span> <span data-ttu-id="a87b1-156">调用时 `SaveChanges` ，实体框架将为其缓存中其状态为 not 的每个实体调用此方法 `Unchanged` 。</span><span class="sxs-lookup"><span data-stu-id="a87b1-156">When you call `SaveChanges`, Entity Framework will call this method for each entity in its cache whose state is not `Unchanged`.</span></span> <span data-ttu-id="a87b1-157">你可以在此处直接放置验证逻辑，甚至可以使用此方法来调用，例如， `Blog.Validate` 上一部分中添加的方法。</span><span class="sxs-lookup"><span data-stu-id="a87b1-157">You can put validation logic directly in here or even use this method to call, for example, the `Blog.Validate` method added in the previous section.</span></span>

<span data-ttu-id="a87b1-158">下面是一个用于 `ValidateEntity` 验证新的替代的示例 `Post` ，以确保尚未使用帖子标题。</span><span class="sxs-lookup"><span data-stu-id="a87b1-158">Here's an example of a `ValidateEntity` override that validates new `Post`s to ensure that the post title hasn't been used already.</span></span> <span data-ttu-id="a87b1-159">它首先检查实体是否为 post 并添加其状态。</span><span class="sxs-lookup"><span data-stu-id="a87b1-159">It first checks to see if the entity is a post and that its state is Added.</span></span> <span data-ttu-id="a87b1-160">如果是这种情况，则会在数据库中查找具有相同标题的帖子。</span><span class="sxs-lookup"><span data-stu-id="a87b1-160">If that's the case, then it looks in the database to see if there is already a post with the same title.</span></span> <span data-ttu-id="a87b1-161">如果已存在现有的公告，则会创建一个新的 `DbEntityValidationResult` 。</span><span class="sxs-lookup"><span data-stu-id="a87b1-161">If there is an existing post already, then a new `DbEntityValidationResult` is created.</span></span>

<span data-ttu-id="a87b1-162">`DbEntityValidationResult``DbEntityEntry` `ICollection<DbValidationErrors>` 为单个实体承载和。</span><span class="sxs-lookup"><span data-stu-id="a87b1-162">`DbEntityValidationResult` houses a `DbEntityEntry` and an `ICollection<DbValidationErrors>` for a single entity.</span></span> <span data-ttu-id="a87b1-163">此方法开始时，将 `DbEntityValidationResult` 实例化，并将发现的任何错误添加到其 `ValidationErrors` 集合中。</span><span class="sxs-lookup"><span data-stu-id="a87b1-163">At the start of this method, a `DbEntityValidationResult` is instantiated and then any errors that are discovered are added into its `ValidationErrors` collection.</span></span>

``` csharp
protected override DbEntityValidationResult ValidateEntity (
    System.Data.Entity.Infrastructure.DbEntityEntry entityEntry,
    IDictionary<object, object> items)
{
    var result = new DbEntityValidationResult(entityEntry, new List<DbValidationError>());

    if (entityEntry.Entity is Post post && entityEntry.State == EntityState.Added)
    {
        // Check for uniqueness of post title
        if (Posts.Where(p => p.Title == post.Title).Any())
        {
            result.ValidationErrors.Add(
                    new System.Data.Entity.Validation.DbValidationError(
                        nameof(Title),
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

## <a name="explicitly-triggering-validation"></a><span data-ttu-id="a87b1-164">显式触发验证</span><span class="sxs-lookup"><span data-stu-id="a87b1-164">Explicitly triggering validation</span></span>

<span data-ttu-id="a87b1-165">调用可 `SaveChanges` 触发本文中所述的所有验证。</span><span class="sxs-lookup"><span data-stu-id="a87b1-165">A call to `SaveChanges` triggers all of the validations covered in this article.</span></span> <span data-ttu-id="a87b1-166">但不需要依赖 `SaveChanges` 。</span><span class="sxs-lookup"><span data-stu-id="a87b1-166">But you don't need to rely on `SaveChanges`.</span></span> <span data-ttu-id="a87b1-167">你可能希望在应用程序中的其他地方进行验证。</span><span class="sxs-lookup"><span data-stu-id="a87b1-167">You may prefer to validate elsewhere in your application.</span></span>

<span data-ttu-id="a87b1-168">`DbContext.GetValidationErrors` 将触发所有验证、批注定义的验证或流畅的 API、在 (中创建的验证（ `IValidatableObject` 例如 `Blog.Validate`) ）以及在方法中执行的验证 `DbContext.ValidateEntity` 。</span><span class="sxs-lookup"><span data-stu-id="a87b1-168">`DbContext.GetValidationErrors` will trigger all of the validations, those defined by annotations or the Fluent API, the validation created in `IValidatableObject` (for example, `Blog.Validate`), and the validations performed in the `DbContext.ValidateEntity` method.</span></span>

<span data-ttu-id="a87b1-169">下面的代码将 `GetValidationErrors` 在的当前实例上调用 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="a87b1-169">The following code will call `GetValidationErrors` on the current instance of a `DbContext`.</span></span> <span data-ttu-id="a87b1-170">`ValidationErrors` 按实体类型分组到中 `DbEntityValidationResult` 。</span><span class="sxs-lookup"><span data-stu-id="a87b1-170">`ValidationErrors` are grouped by entity type into `DbEntityValidationResult`.</span></span> <span data-ttu-id="a87b1-171">此代码首先遍历 `DbEntityValidationResult` 方法返回的，然后遍历其中的每个 `DbValidationError` 。</span><span class="sxs-lookup"><span data-stu-id="a87b1-171">The code iterates first through the `DbEntityValidationResult`s returned by the method and then through each `DbValidationError` inside.</span></span>

``` csharp
foreach (var validationResult in db.GetValidationErrors())
{
    foreach (var error in validationResult.ValidationErrors)
    {
        Debug.WriteLine(
            "Entity Property: {0}, Error {1}",
            error.PropertyName,
            error.ErrorMessage);
    }
}
```

## <a name="other-considerations-when-using-validation"></a><span data-ttu-id="a87b1-172">使用验证时的其他注意事项</span><span class="sxs-lookup"><span data-stu-id="a87b1-172">Other considerations when using validation</span></span>

<span data-ttu-id="a87b1-173">下面是在使用实体框架验证时要考虑的一些其他事项：</span><span class="sxs-lookup"><span data-stu-id="a87b1-173">Here are a few other points to consider when using Entity Framework validation:</span></span>

- <span data-ttu-id="a87b1-174">在验证过程中禁用延迟加载</span><span class="sxs-lookup"><span data-stu-id="a87b1-174">Lazy loading is disabled during validation</span></span>
- <span data-ttu-id="a87b1-175">EF 将验证未映射到数据库中的列 (属性的非映射属性上的数据批注) </span><span class="sxs-lookup"><span data-stu-id="a87b1-175">EF will validate data annotations on non-mapped properties (properties that are not mapped to a column in the database)</span></span>
- <span data-ttu-id="a87b1-176">验证在期间检测到更改后执行 `SaveChanges` 。</span><span class="sxs-lookup"><span data-stu-id="a87b1-176">Validation is performed after changes are detected during `SaveChanges`.</span></span> <span data-ttu-id="a87b1-177">如果在验证期间进行更改，则需要负责通知更改跟踪器</span><span class="sxs-lookup"><span data-stu-id="a87b1-177">If you make changes during validation it is your responsibility to notify the change tracker</span></span>
- <span data-ttu-id="a87b1-178">`DbUnexpectedValidationException` 如果在验证期间发生错误，则会引发。</span><span class="sxs-lookup"><span data-stu-id="a87b1-178">`DbUnexpectedValidationException` is thrown if errors occur during validation</span></span>
- <span data-ttu-id="a87b1-179">实体框架包括在模型中的方面 (最大长度、必需等，) 将导致验证，即使类和/或使用 EF 设计器创建模型时也是如此？</span><span class="sxs-lookup"><span data-stu-id="a87b1-179">Facets that Entity Framework includes in the model (maximum length, required, etc.) will cause validation, even if there are no data annotations in your classes and/or you used the EF Designer to create your model</span></span>
- <span data-ttu-id="a87b1-180">优先规则：</span><span class="sxs-lookup"><span data-stu-id="a87b1-180">Precedence rules:</span></span>
  - <span data-ttu-id="a87b1-181">流畅的 API 调用替代相应的数据批注</span><span class="sxs-lookup"><span data-stu-id="a87b1-181">Fluent API calls override the corresponding data annotations</span></span>
- <span data-ttu-id="a87b1-182">执行顺序：</span><span class="sxs-lookup"><span data-stu-id="a87b1-182">Execution order:</span></span>
  - <span data-ttu-id="a87b1-183">属性验证发生在类型验证之前</span><span class="sxs-lookup"><span data-stu-id="a87b1-183">Property validation occurs before type validation</span></span>
  - <span data-ttu-id="a87b1-184">仅当属性验证成功时才发生类型验证</span><span class="sxs-lookup"><span data-stu-id="a87b1-184">Type validation only occurs if property validation succeeds</span></span>
- <span data-ttu-id="a87b1-185">如果某个属性是复杂的，则其验证也包括：</span><span class="sxs-lookup"><span data-stu-id="a87b1-185">If a property is complex, its validation will also include:</span></span>
  - <span data-ttu-id="a87b1-186">针对复杂类型属性的属性级验证</span><span class="sxs-lookup"><span data-stu-id="a87b1-186">Property-level validation on the complex type properties</span></span>
  - <span data-ttu-id="a87b1-187">对复杂类型的类型级别验证，包括 `IValidatableObject` 对复杂类型的验证</span><span class="sxs-lookup"><span data-stu-id="a87b1-187">Type level validation on the complex type, including `IValidatableObject` validation on the complex type</span></span>

## <a name="summary"></a><span data-ttu-id="a87b1-188">摘要</span><span class="sxs-lookup"><span data-stu-id="a87b1-188">Summary</span></span>

<span data-ttu-id="a87b1-189">实体框架中的验证 API 与 MVC 中的客户端验证非常完美，但你不必依赖于客户端验证。</span><span class="sxs-lookup"><span data-stu-id="a87b1-189">The validation API in Entity Framework plays very nicely with client side validation in MVC but you don't have to rely on client-side validation.</span></span> <span data-ttu-id="a87b1-190">实体框架将在服务器端对 DataAnnotations 或使用 code first 流畅 API 应用的配置进行验证。</span><span class="sxs-lookup"><span data-stu-id="a87b1-190">Entity Framework will take care of the validation on the server side for DataAnnotations or configurations you've applied with the code first Fluent API.</span></span>

<span data-ttu-id="a87b1-191">你还看到了许多扩展点，用于自定义该行为，无论你使用 `IValidatableObject` 接口还是点击 `DbContext.ValidateEntity` 方法。</span><span class="sxs-lookup"><span data-stu-id="a87b1-191">You also saw a number of extensibility points for customizing the behavior whether you use the `IValidatableObject` interface or tap into the `DbContext.ValidateEntity` method.</span></span> <span data-ttu-id="a87b1-192">可以通过使用这两种验证方法 `DbContext` ，无论你使用 Code First、Model First 还是 Database First 工作流来描述你的概念模型。</span><span class="sxs-lookup"><span data-stu-id="a87b1-192">And these last two means of validation are available through the `DbContext`, whether you use the Code First, Model First or Database First workflow to describe your conceptual model.</span></span>
