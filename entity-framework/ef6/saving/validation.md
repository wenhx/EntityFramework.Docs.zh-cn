---
title: 验证-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 77d6a095-c0d0-471e-80b9-8f9aea6108b2
ms.openlocfilehash: 2c5e6f1b3f60862124bafcac42e8859a7591f8e6
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414367"
---
# <a name="data-validation"></a>数据验证
> [!NOTE]
> **仅限 ef 4.1** -在实体框架4.1 中引入了本页中所述的功能、api 等。 如果使用的是早期版本，则不会应用部分或全部信息

此页面上的内容适用于最初由 Julie Lerman （[https://thedatafarm.com](https://thedatafarm.com)）编写的文章。

实体框架提供了一种非常丰富的验证功能，这些功能可用于向用户界面提供客户端验证或用于服务器端验证。 使用 code first 时，可以使用批注或 Fluent API 配置来指定验证。 可以在代码中指定其他验证和更复杂的验证，无论模型是从代码 hails、模型优先还是数据库优先进行操作，都可以使用。

## <a name="the-model"></a>模型

我将使用一对简单的类来演示验证：博客和文章。

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

## <a name="data-annotations"></a>数据注释

Code First 使用 `System.ComponentModel.DataAnnotations` 程序集中的批注作为配置代码优先类的一种方法。 这些批注中包括 `Required`、`MaxLength` 和 `MinLength`等规则。 许多 .NET 客户端应用程序还识别这些批注，例如，ASP.NET MVC。 您可以通过这些批注实现客户端和服务器端验证。 例如，你可以强制 "博客 Title" 属性为必需属性。

``` csharp
[Required]
public string Title { get; set; }
```

如果应用程序中没有额外的代码或标记更改，则现有 MVC 应用程序会执行客户端验证，甚至使用属性和批注名称动态生成消息。

![图 1](~/ef6/media/figure01.png)

在此 "创建" 视图的 "回发" 方法中，实体框架用于将新的博客保存到数据库中，但在应用程序到达该代码之前触发了 MVC 的客户端验证。

不过，客户端验证不是项目符号。 用户可能会影响浏览器的功能或更糟的功能，黑客可能会使用某些 trickery 来避免 UI 验证。 但实体框架还将识别 `Required` 注释并对其进行验证。

对此进行测试的一种简单方法是禁用 MVC 的客户端验证功能。 可以在 MVC 应用程序的 web.config 文件中执行此操作。 AppSettings 节具有 ClientValidationEnabled 的键。 将此项设置为 false 将阻止 UI 执行验证。

``` xml
<appSettings>
    <add key="ClientValidationEnabled"value="false"/>
    ...
</appSettings>
```

即使已禁用客户端验证，也会在应用程序中获得相同的响应。 错误消息 "需要标题字段" 将像以前一样显示。 除非现在是服务器端验证的结果。 实体框架将在 `Required` 注释上执行验证（甚至在麻烦之前生成用于发送到数据库的 `INSERT` 命令），并将错误返回到将显示消息的 MVC。

## <a name="fluent-api"></a>Fluent API

您可以使用 code first 的 Fluent API 而不是使用批注来获取相同的客户端 & 服务器端验证。 我将使用 MaxLength 验证来说明这一点，而不是使用 `Required`。

当代码优先从类生成模型时，将应用 "熟知 API 配置"。 可以通过重写 DbContext 类的 OnModelCreating 方法注入配置。 以下配置指定 BloggerName 属性的长度不能超过10个字符。

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

基于流畅 API 配置引发的验证错误不会自动到达 UI，但你可以在代码中捕获该错误，然后相应地对其进行响应。

下面是应用程序的 BlogController 类中的一些异常处理错误代码，该代码在实体框架尝试使用超过10个字符的 BloggerName 保存博客时捕获验证错误。

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

验证不会自动传递回视图，这就是使用 `ModelState.AddModelError` 的其他代码的原因。 这可以确保错误详细信息使其成为视图，然后将使用 `ValidationMessageFor` Htmlhelper 显示错误。

``` csharp
@Html.ValidationMessageFor(model => model.BloggerName)
```

## <a name="ivalidatableobject"></a>IValidatableObject

`IValidatableObject` 是 `System.ComponentModel.DataAnnotations`的接口。 尽管它不是实体框架 API 的一部分，但仍可在实体框架类中利用它进行服务器端验证。 `IValidatableObject` 提供实体框架将在 SaveChanges 期间调用的 `Validate` 方法，你可以随时调用以验证类。

`Required` 和 `MaxLength` 等配置对单个字段执行验证。 在 `Validate` 方法中，可以使用更复杂的逻辑，例如，比较两个字段。

在下面的示例中，扩展了 `Blog` 类以实现 `IValidatableObject`，然后提供 `Title` 和 `BloggerName` 不能匹配的规则。

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

`ValidationResult` 构造函数采用一个表示错误消息的 `string`，以及一个 `string`的数组，该数组表示与验证关联的成员名称。 由于此验证同时检查 `Title` 和 `BloggerName`，因此将返回这两个属性名。

与 "流畅 API" 提供的验证不同，此验证结果将被视图识别，并且之前用于将错误添加到 `ModelState` 中的异常处理程序不是必需的。 因为我在 `ValidationResult`中设置了两个属性名，所以 MVC HtmlHelpers 将显示这两个属性的错误消息。

![图2](~/ef6/media/figure02.png)

## <a name="dbcontextvalidateentity"></a>DbContext.ValidateEntity

`DbContext` 具有称为 `ValidateEntity`的可重写方法。 调用 `SaveChanges`时，实体框架将为其缓存中其状态为 "不 `Unchanged`" 的每个实体调用此方法。 您可以在此处直接放置验证逻辑，甚至可以使用此方法来调用，例如，在上一节中添加的 `Blog.Validate` 方法。

下面是一个用于验证新的 `Post`的 `ValidateEntity` 重写示例，以确保尚未使用帖子标题。 它首先检查实体是否为 post 并添加其状态。 如果是这种情况，则会在数据库中查找具有相同标题的帖子。 如果已存在现有的公告，则会创建一个新的 `DbEntityValidationResult`。

`DbEntityValidationResult` 承载单个实体的 `DbEntityEntry` 和 `ICollection<DbValidationErrors>`。 此方法开始时，实例化 `DbEntityValidationResult`，然后将发现的任何错误添加到其 `ValidationErrors` 集合。

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

## <a name="explicitly-triggering-validation"></a>显式触发验证

对 `SaveChanges` 的调用会触发本文中所述的所有验证。 但不需要依赖 `SaveChanges`。 你可能希望在应用程序中的其他地方进行验证。

`DbContext.GetValidationErrors` 将触发所有验证、批注定义的验证或流畅的 API、在 `IValidatableObject` 中创建的验证（例如 `Blog.Validate`）以及在 `DbContext.ValidateEntity` 方法中执行的验证。

下面的代码将对 `DbContext`的当前实例调用 `GetValidationErrors`。 `ValidationErrors` 按实体类型分组到 `DbEntityValidationResult`中。 此代码首先遍历方法返回的 `DbEntityValidationResult`，然后遍历其中的每个 `DbValidationError`。

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

## <a name="other-considerations-when-using-validation"></a>使用验证时的其他注意事项

下面是在使用实体框架验证时要考虑的一些其他事项：

- 在验证过程中禁用延迟加载
- EF 将验证非映射属性（未映射到数据库中的列的属性）中的数据批注
- 在 `SaveChanges`期间检测到更改后，将执行验证。 如果在验证期间进行更改，则需要负责通知更改跟踪器
- 如果在验证期间发生错误，则会引发 `DbUnexpectedValidationException`
- 实体框架包括在模型中的方面（最大长度、必需等）将导致验证，即使类和/或使用 EF 设计器创建模型
- 优先规则：
  - 流畅的 API 调用替代相应的数据批注
- 执行顺序：
  - 属性验证发生在类型验证之前
  - 仅当属性验证成功时才发生类型验证
- 如果某个属性是复杂的，则其验证也包括：
  - 针对复杂类型属性的属性级验证
  - 对复杂类型的类型级别验证，包括对复杂类型 `IValidatableObject` 验证

## <a name="summary"></a>摘要

实体框架中的验证 API 与 MVC 中的客户端验证非常完美，但你不必依赖于客户端验证。 实体框架将在服务器端对 DataAnnotations 或使用 code first 流畅 API 应用的配置进行验证。

你还看到了许多扩展点，用于自定义该行为，无论你使用 `IValidatableObject` 接口还是点击 `DbContext.ValidateEntity` 方法。 无论是使用 Code First、Model First 还是 Database First 工作流来描述概念模型，最后两种验证方法都可通过 `DbContext`来使用。
