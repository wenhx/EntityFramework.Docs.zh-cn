---
title: Code First 数据批注-EF6
description: 实体框架6中的 Code First 数据批注
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/data-annotations
ms.openlocfilehash: dd91ddf674f2235190e50beb847e569a898ad59b
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90074059"
---
# <a name="code-first-data-annotations"></a><span data-ttu-id="fe4c7-103">Code First 数据批注</span><span class="sxs-lookup"><span data-stu-id="fe4c7-103">Code First Data Annotations</span></span>
> [!NOTE]
> <span data-ttu-id="fe4c7-104">**仅限 ef 4.1** -在实体框架4.1 中引入了本页中所述的功能、api 等。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-104">**EF4.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 4.1.</span></span> <span data-ttu-id="fe4c7-105">如果你使用的是早期版本，则不会应用此信息中的部分或全部。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-105">If you are using an earlier version, some or all of this information does not apply.</span></span>

<span data-ttu-id="fe4c7-106">此页面上的内容适用于最初由 Julie Lerman () 编写的文章 \<http://thedatafarm.com> 。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-106">The content on this page is adapted from an article originally written by Julie Lerman (\<http://thedatafarm.com>).</span></span>

<span data-ttu-id="fe4c7-107">利用实体框架 Code First，你可以使用自己的域类来表示 EF 依赖来执行查询、更改跟踪和更新功能的模型。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-107">Entity Framework Code First allows you to use your own domain classes to represent the model that EF relies on to perform querying, change tracking, and updating functions.</span></span> <span data-ttu-id="fe4c7-108">Code First 利用称为 "约定 over 配置" 的编程模式。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-108">Code First leverages a programming pattern referred to as 'convention over configuration.'</span></span> <span data-ttu-id="fe4c7-109">Code First 将假设你的类遵循实体框架的约定，在这种情况下，将自动处理如何执行其作业。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-109">Code First will assume that your classes follow the conventions of Entity Framework, and in that case, will automatically work out how to perform its job.</span></span> <span data-ttu-id="fe4c7-110">但是，如果你的类不遵循这些约定，则可以将配置添加到你的类，以便为 EF 提供必需的信息。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-110">However, if your classes do not follow those conventions, you have the ability to add configurations to your classes to provide EF with the requisite information.</span></span>

<span data-ttu-id="fe4c7-111">Code First 提供了向你的类添加这些配置的两种方法。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-111">Code First gives you two ways to add these configurations to your classes.</span></span> <span data-ttu-id="fe4c7-112">其中一种方法是使用名为 DataAnnotations 的简单特性，第二种方法是使用 Code First 的流畅 API，这为你提供了一种在代码中以强制方式描述配置的方式。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-112">One is using simple attributes called DataAnnotations, and the second is using Code First’s Fluent API, which provides you with a way to describe configurations imperatively, in code.</span></span>

<span data-ttu-id="fe4c7-113">本文重点介绍如何使用 DataAnnotations 命名空间) 中的 DataAnnotations (来配置类-突出显示最常用的配置。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-113">This article will focus on using DataAnnotations (in the System.ComponentModel.DataAnnotations namespace) to configure your classes – highlighting the most commonly needed configurations.</span></span> <span data-ttu-id="fe4c7-114">DataAnnotations 也可由许多 .NET 应用程序（如 ASP.NET MVC）理解，这允许这些应用程序利用相同的注释进行客户端验证。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-114">DataAnnotations are also understood by a number of .NET applications, such as ASP.NET MVC which allows these applications to leverage the same annotations for client-side validations.</span></span>


## <a name="the-model"></a><span data-ttu-id="fe4c7-115">模型</span><span class="sxs-lookup"><span data-stu-id="fe4c7-115">The model</span></span>

<span data-ttu-id="fe4c7-116">我将使用简单的类对 Code First DataAnnotations 进行演示：博客和文章。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-116">I’ll demonstrate Code First DataAnnotations with a simple pair of classes: Blog and Post.</span></span>

``` csharp
    public class Blog
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string BloggerName { get; set;}
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

<span data-ttu-id="fe4c7-117">正如他们一样，博客和文章类可以方便地遵循 code first 约定，无需进行调整即可启用 EF 兼容性。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-117">As they are, the Blog and Post classes conveniently follow code first convention and require no tweaks to enable EF compatability.</span></span> <span data-ttu-id="fe4c7-118">但是，您还可以使用批注向 EF 提供有关它们所映射到的类和数据库的详细信息。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-118">However, you can also use the annotations to provide more information to EF about the classes and the database to which they map.</span></span>

 

## <a name="key"></a><span data-ttu-id="fe4c7-119">Key</span><span class="sxs-lookup"><span data-stu-id="fe4c7-119">Key</span></span>

<span data-ttu-id="fe4c7-120">实体框架依赖于每个实体，每个实体都有一个用于实体跟踪的键值。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-120">Entity Framework relies on every entity having a key value that is used for entity tracking.</span></span> <span data-ttu-id="fe4c7-121">Code First 的一种约定是隐式键属性;Code First 将查找名为 "Id" 的属性，或者查找类名称和 "Id" （如 "BlogId"）的组合。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-121">One convention of Code First is implicit key properties; Code First will look for a property named “Id”, or a combination of class name and “Id”, such as “BlogId”.</span></span> <span data-ttu-id="fe4c7-122">此属性将映射到数据库中的主键列。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-122">This property will map to a primary key column in the database.</span></span>

<span data-ttu-id="fe4c7-123">博客和帖子类都遵循此约定。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-123">The Blog and Post classes both follow this convention.</span></span> <span data-ttu-id="fe4c7-124">如果不是，怎么办？</span><span class="sxs-lookup"><span data-stu-id="fe4c7-124">What if they didn’t?</span></span> <span data-ttu-id="fe4c7-125">如果博客使用的是名称 *PrimaryTrackingKey* （甚至是 *foo*），该怎么办？</span><span class="sxs-lookup"><span data-stu-id="fe4c7-125">What if Blog used the name *PrimaryTrackingKey* instead, or even *foo*?</span></span> <span data-ttu-id="fe4c7-126">如果代码优先找不到与此约定相匹配的属性，则会引发异常，因为实体框架要求必须有一个键属性。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-126">If code first does not find a property that matches this convention it will throw an exception because of Entity Framework’s requirement that you must have a key property.</span></span> <span data-ttu-id="fe4c7-127">你可以使用密钥批注来指定要用作 EntityKey 的属性。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-127">You can use the key annotation to specify which property is to be used as the EntityKey.</span></span>

``` csharp
    public class Blog
    {
        [Key]
        public int PrimaryTrackingKey { get; set; }
        public string Title { get; set; }
        public string BloggerName { get; set;}
        public virtual ICollection<Post> Posts { get; set; }
    }
```

<span data-ttu-id="fe4c7-128">如果使用代码优先的数据库生成功能，博客表将具有名为 PrimaryTrackingKey 的主键列，默认情况下，此列也定义为标识。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-128">If you are using code first’s database generation feature, the Blog table will have a primary key column named PrimaryTrackingKey, which is also defined as Identity by default.</span></span>

![带主键的博客表](~/ef6/media/jj591583-figure01.png)

### <a name="composite-keys"></a><span data-ttu-id="fe4c7-130">组合键</span><span class="sxs-lookup"><span data-stu-id="fe4c7-130">Composite keys</span></span>

<span data-ttu-id="fe4c7-131">实体框架支持组合键-由多个属性组成的主键。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-131">Entity Framework supports composite keys - primary keys that consist of more than one property.</span></span> <span data-ttu-id="fe4c7-132">例如，你可以有一个 Passport 类，其 primary key 是 PassportNumber 和 IssuingCountry 的组合。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-132">For example, you could have a Passport class whose primary key is a combination of PassportNumber and IssuingCountry.</span></span>

``` csharp
    public class Passport
    {
        [Key]
        public int PassportNumber { get; set; }
        [Key]
        public string IssuingCountry { get; set; }
        public DateTime Issued { get; set; }
        public DateTime Expires { get; set; }
    }
```

<span data-ttu-id="fe4c7-133">尝试在 EF 模型中使用以上类将导致 `InvalidOperationException` ：</span><span class="sxs-lookup"><span data-stu-id="fe4c7-133">Attempting to use the above class in your EF model would result in an `InvalidOperationException`:</span></span>

<span data-ttu-id="fe4c7-134">*无法确定类型 "Passport" 的组合键。使用 ColumnAttribute 或 HasKey 方法为组合主键指定顺序。*</span><span class="sxs-lookup"><span data-stu-id="fe4c7-134">*Unable to determine composite primary key ordering for type 'Passport'. Use the ColumnAttribute or the HasKey method to specify an order for composite primary keys.*</span></span>

<span data-ttu-id="fe4c7-135">若要使用组合键，实体框架要求您定义键属性的顺序。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-135">In order to use composite keys, Entity Framework requires you to define an order for the key properties.</span></span> <span data-ttu-id="fe4c7-136">为此，可以使用列批注指定顺序。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-136">You can do this by using the Column annotation to specify an order.</span></span>

>[!NOTE]
> <span data-ttu-id="fe4c7-137">顺序值是相对 (，而不是基于索引) 因此可以使用任何值。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-137">The order value is relative (rather than index based) so any values can be used.</span></span> <span data-ttu-id="fe4c7-138">例如，100和200可接受而不是1和2。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-138">For example, 100 and 200 would be acceptable in place of 1 and 2.</span></span>

``` csharp
    public class Passport
    {
        [Key]
        [Column(Order=1)]
        public int PassportNumber { get; set; }
        [Key]
        [Column(Order = 2)]
        public string IssuingCountry { get; set; }
        public DateTime Issued { get; set; }
        public DateTime Expires { get; set; }
    }
```

<span data-ttu-id="fe4c7-139">如果有包含复合外键的实体，则必须指定用于相应主键属性的相同列排序。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-139">If you have entities with composite foreign keys, then you must specify the same column ordering that you used for the corresponding primary key properties.</span></span>

<span data-ttu-id="fe4c7-140">只有外键属性中的相对顺序需要相同，分配给 **Order** 的确切值不需要匹配。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-140">Only the relative ordering within the foreign key properties needs to be the same, the exact values assigned to **Order** do not need to match.</span></span> <span data-ttu-id="fe4c7-141">例如，在下面的类中，可以使用3和4代替1和2。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-141">For example, in the following class, 3 and 4 could be used in place of 1 and 2.</span></span>

``` csharp
    public class PassportStamp
    {
        [Key]
        public int StampId { get; set; }
        public DateTime Stamped { get; set; }
        public string StampingCountry { get; set; }

        [ForeignKey("Passport")]
        [Column(Order = 1)]
        public int PassportNumber { get; set; }

        [ForeignKey("Passport")]
        [Column(Order = 2)]
        public string IssuingCountry { get; set; }

        public Passport Passport { get; set; }
    }
```

## <a name="required"></a><span data-ttu-id="fe4c7-142">必需</span><span class="sxs-lookup"><span data-stu-id="fe4c7-142">Required</span></span>

<span data-ttu-id="fe4c7-143">`Required`批注告知 EF 需要特定属性。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-143">The `Required` annotation tells EF that a particular property is required.</span></span>

<span data-ttu-id="fe4c7-144">向 Title 属性添加所需的将强制 EF (和 MVC) ，以确保属性中包含数据。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-144">Adding Required to the Title property will force EF (and MVC) to ensure that the property has data in it.</span></span>

``` csharp
    [Required]
    public string Title { get; set; }
```

<span data-ttu-id="fe4c7-145">如果应用程序中没有额外的代码或标记更改，MVC 应用程序将执行客户端验证，甚至使用属性和批注名称动态生成消息。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-145">With no additional code or markup changes in the application, an MVC application will perform client side validation, even dynamically building a message using the property and annotation names.</span></span>

![需要标题的 "创建页面" 错误](~/ef6/media/jj591583-figure02.png)

<span data-ttu-id="fe4c7-147">必需的属性还会影响生成的数据库，方法是将映射的属性设为不可为 null。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-147">The Required attribute will also affect the generated database by making the mapped property non-nullable.</span></span> <span data-ttu-id="fe4c7-148">请注意，Title 字段已更改为 "not null"。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-148">Notice that the Title field has changed to “not null”.</span></span>

>[!NOTE]
> <span data-ttu-id="fe4c7-149">在某些情况下，即使属性是必需的，也无法使数据库中的列不可为 null。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-149">In some cases it may not be possible for the column in the database to be non-nullable even though the property is required.</span></span> <span data-ttu-id="fe4c7-150">例如，对多个类型使用 TPH 继承战略数据时，会将其存储在一个表中。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-150">For example, when using a TPH inheritance strategy data for multiple types is stored in a single table.</span></span> <span data-ttu-id="fe4c7-151">如果派生的类型包含所需的属性，则列不能为 null，因为并不是层次结构中的所有类型都具有此属性。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-151">If a derived type includes a required property the column cannot be made non-nullable since not all types in the hierarchy will have this property.</span></span>

 

![博客表](~/ef6/media/jj591583-figure03.png)

 

## <a name="maxlength-and-minlength"></a><span data-ttu-id="fe4c7-153">MaxLength 和 MinLength</span><span class="sxs-lookup"><span data-stu-id="fe4c7-153">MaxLength and MinLength</span></span>

<span data-ttu-id="fe4c7-154">`MaxLength`和 `MinLength` 特性使你可以指定其他属性验证，就像对执行的操作一样 `Required` 。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-154">The `MaxLength` and `MinLength` attributes allow you to specify additional property validations, just as you did with `Required`.</span></span>

<span data-ttu-id="fe4c7-155">下面是具有长度要求的 BloggerName。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-155">Here is the BloggerName with length requirements.</span></span> <span data-ttu-id="fe4c7-156">该示例还演示了如何合并特性。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-156">The example also demonstrates how to combine attributes.</span></span>

``` csharp
    [MaxLength(10),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="fe4c7-157">MaxLength 批注将属性的长度设置为10，将影响数据库。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-157">The MaxLength annotation will impact the database by setting the property’s length to 10.</span></span>

![显示 BloggerName 列的最大长度的博客表格](~/ef6/media/jj591583-figure04.png)

<span data-ttu-id="fe4c7-159">MVC 客户端批注和 EF 4.1 服务器端批注都将接受此验证，并再次动态生成错误消息： "字段 BloggerName 必须是最大长度为" 10 "的字符串或数组类型。"该消息只需很长时间。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-159">MVC client-side annotation and EF 4.1 server-side annotation will both honor this validation, again dynamically building an error message: “The field BloggerName must be a string or array type with a maximum length of '10'.” That message is a little long.</span></span> <span data-ttu-id="fe4c7-160">许多批注允许您使用 ErrorMessage 特性指定错误消息。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-160">Many annotations let you specify an error message with the ErrorMessage attribute.</span></span>

``` csharp
    [MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="fe4c7-161">您还可以在所需的批注中指定 ErrorMessage。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-161">You can also specify ErrorMessage in the Required annotation.</span></span>

![创建具有自定义错误消息的页面](~/ef6/media/jj591583-figure05.png)

 

## <a name="notmapped"></a><span data-ttu-id="fe4c7-163">NotMapped</span><span class="sxs-lookup"><span data-stu-id="fe4c7-163">NotMapped</span></span>

<span data-ttu-id="fe4c7-164">Code first 约定规定每个属于受支持数据类型的属性都在数据库中表示。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-164">Code first convention dictates that every property that is of a supported data type is represented in the database.</span></span> <span data-ttu-id="fe4c7-165">但在应用程序中并不总是如此。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-165">But this isn’t always the case in your applications.</span></span> <span data-ttu-id="fe4c7-166">例如，你可能在博客类中有一个属性，该属性基于 "标题" 和 "BloggerName" 字段创建代码。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-166">For example you might have a property in the Blog class that creates a code based on the Title and BloggerName fields.</span></span> <span data-ttu-id="fe4c7-167">该属性可以动态创建，不需要存储。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-167">That property can be created dynamically and does not need to be stored.</span></span> <span data-ttu-id="fe4c7-168">可以用 NotMapped 批注（如此 BlogCode 属性）来标记不映射到数据库的任何属性。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-168">You can mark any properties that do not map to the database with the NotMapped annotation such as this BlogCode property.</span></span>

``` csharp
    [NotMapped]
    public string BlogCode
    {
        get
        {
            return Title.Substring(0, 1) + ":" + BloggerName.Substring(0, 1);
        }
    }
```

 

## <a name="complextype"></a><span data-ttu-id="fe4c7-169">ComplexType</span><span class="sxs-lookup"><span data-stu-id="fe4c7-169">ComplexType</span></span>

<span data-ttu-id="fe4c7-170">请不要在一组类中描述域实体，然后将这些类分层以描述完整的实体。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-170">It’s not uncommon to describe your domain entities across a set of classes and then layer those classes to describe a complete entity.</span></span> <span data-ttu-id="fe4c7-171">例如，可以将名为 BlogDetails 的类添加到模型。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-171">For example, you may add a class called BlogDetails to your model.</span></span>

``` csharp
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

<span data-ttu-id="fe4c7-172">请注意，没有 `BlogDetails` 任何类型的键属性。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-172">Notice that `BlogDetails` does not have any type of key property.</span></span> <span data-ttu-id="fe4c7-173">在域驱动设计中， `BlogDetails` 称为值对象。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-173">In domain driven design, `BlogDetails` is referred to as a value object.</span></span> <span data-ttu-id="fe4c7-174">实体框架将值对象引用为复杂类型。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-174">Entity Framework refers to value objects as complex types.</span></span><span data-ttu-id="fe4c7-175">不能自行跟踪复杂类型。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-175">  Complex types cannot be tracked on their own.</span></span>

<span data-ttu-id="fe4c7-176">但作为类中的属性 `Blog` ， `BlogDetails` 将作为对象的一部分进行跟踪 `Blog` 。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-176">However as a property in the `Blog` class, `BlogDetails` will be tracked as part of a `Blog` object.</span></span> <span data-ttu-id="fe4c7-177">为了使代码优先识别此项，必须将 `BlogDetails` 类标记为 `ComplexType` 。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-177">In order for code first to recognize this, you must mark the `BlogDetails` class as a `ComplexType`.</span></span>

``` csharp
    [ComplexType]
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

<span data-ttu-id="fe4c7-178">现在，你可以在类中添加一个属性 `Blog` ，用于表示 `BlogDetails` 该博客的。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-178">Now you can add a property in the `Blog` class to represent the `BlogDetails` for that blog.</span></span>

``` csharp
        public BlogDetails BlogDetail { get; set; }
```

<span data-ttu-id="fe4c7-179">在数据库中， `Blog` 表将包含博客的所有属性，包括其属性中包含的属性 `BlogDetail` 。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-179">In the database, the `Blog` table will contain all of the properties of the blog including the properties contained in its `BlogDetail` property.</span></span> <span data-ttu-id="fe4c7-180">默认情况下，每个名称前面都带有复杂类型 "BlogDetail" 的名称。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-180">By default, each one is preceded with the name of the complex type, "BlogDetail".</span></span>

![包含复杂类型的博客表](~/ef6/media/jj591583-figure06.png)


## <a name="concurrencycheck"></a><span data-ttu-id="fe4c7-182">ConcurrencyCheck</span><span class="sxs-lookup"><span data-stu-id="fe4c7-182">ConcurrencyCheck</span></span>

<span data-ttu-id="fe4c7-183">`ConcurrencyCheck`使用批注，可以标记一个或多个属性，以便在用户编辑或删除实体时，将该属性用于数据库中的并发检查。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-183">The `ConcurrencyCheck` annotation allows you to flag one or more properties to be used for concurrency checking in the database when a user edits or deletes an entity.</span></span> <span data-ttu-id="fe4c7-184">如果你使用的是 EF 设计器，则这与将属性设置为对齐 `ConcurrencyMode` `Fixed` 。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-184">If you've been working with the EF Designer, this aligns with setting a property's `ConcurrencyMode` to `Fixed`.</span></span>

<span data-ttu-id="fe4c7-185">让我们 `ConcurrencyCheck` 通过将其添加到属性来了解其工作原理 `BloggerName` 。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-185">Let’s see how `ConcurrencyCheck` works by adding it to the `BloggerName` property.</span></span>

``` csharp
    [ConcurrencyCheck, MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="fe4c7-186">当 `SaveChanges` 调用时，由于 `ConcurrencyCheck` 字段上的批注 `BloggerName` ，将在更新中使用该属性的原始值。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-186">When `SaveChanges` is called, because of the `ConcurrencyCheck` annotation on the `BloggerName` field, the original value of that property will be used in the update.</span></span> <span data-ttu-id="fe4c7-187">命令不仅通过筛选键值，还会尝试查找正确的行，而是对的原始值进行筛选 `BloggerName` 。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-187">The command will attempt to locate the correct row by filtering not only on the key value but also on the original value of `BloggerName`.</span></span><span data-ttu-id="fe4c7-188">下面是发送到数据库的 UPDATE 命令的关键部分，您可以在其中看到命令将更新包含1的行 `PrimaryTrackingKey` 和 "Julie" 的行， `BloggerName` 这是从数据库中检索到该博客时的原始值。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-188">  Here are the critical parts of the UPDATE command sent to the database, where you can see the command will update the row that has a `PrimaryTrackingKey` is 1 and a `BloggerName` of “Julie” which was the original value when that blog was retrieved from the database.</span></span>

``` SQL
    where (([PrimaryTrackingKey] = @4) and ([BloggerName] = @5))
    @4=1,@5=N'Julie'
```

<span data-ttu-id="fe4c7-189">如果用户同时更改了博客的博客名称，则此更新将会失败，并且你将收到需要处理的 **DbUpdateConcurrencyException** 。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-189">If someone has changed the blogger name for that blog in the meantime, this update will fail and you’ll get a **DbUpdateConcurrencyException** that you'll need to handle.</span></span>

 

## <a name="timestamp"></a><span data-ttu-id="fe4c7-190">时间戳</span><span class="sxs-lookup"><span data-stu-id="fe4c7-190">TimeStamp</span></span>

<span data-ttu-id="fe4c7-191">更常见的情况是使用 rowversion 或时间戳字段进行并发检查。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-191">It's more common to use rowversion or timestamp fields for concurrency checking.</span></span> <span data-ttu-id="fe4c7-192">但不使用 `ConcurrencyCheck` 批注， `TimeStamp` 只要属性的类型是字节数组，就可以使用更具体的批注。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-192">But rather than using the `ConcurrencyCheck` annotation, you can use the more specific `TimeStamp` annotation as long as the type of the property is byte array.</span></span> <span data-ttu-id="fe4c7-193">代码优先会将 `Timestamp` 属性视为与 `ConcurrencyCheck` 属性相同，但它还将确保代码第一个生成的数据库字段不可为 null。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-193">Code first will treat `Timestamp` properties the same as `ConcurrencyCheck` properties, but it will also ensure that the database field that code first generates is non-nullable.</span></span> <span data-ttu-id="fe4c7-194">给定的类中只能有一个时间戳属性。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-194">You can only have one timestamp property in a given class.</span></span>

<span data-ttu-id="fe4c7-195">将以下属性添加到博客类：</span><span class="sxs-lookup"><span data-stu-id="fe4c7-195">Adding the following property to the Blog class:</span></span>

``` csharp
    [Timestamp]
    public Byte[] TimeStamp { get; set; }
```

<span data-ttu-id="fe4c7-196">在数据库表中生成一个不可以为 null 的时间戳列的代码。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-196">results in code first creating a non-nullable timestamp column in the database table.</span></span>

![带有时间戳列的博客表](~/ef6/media/jj591583-figure07.png)

 

## <a name="table-and-column"></a><span data-ttu-id="fe4c7-198">表和列</span><span class="sxs-lookup"><span data-stu-id="fe4c7-198">Table and Column</span></span>

<span data-ttu-id="fe4c7-199">如果允许 Code First 创建数据库，则可能需要更改要创建的表和列的名称。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-199">If you are letting Code First create the database, you may want to change the name of the tables and columns it is creating.</span></span> <span data-ttu-id="fe4c7-200">您还可以将 Code First 与现有数据库一起使用。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-200">You can also use Code First with an existing database.</span></span> <span data-ttu-id="fe4c7-201">但并非总是域中类和属性的名称与数据库中的表和列的名称相匹配。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-201">But it's not always the case that the names of the classes and properties in your domain match the names of the tables and columns in your database.</span></span>

<span data-ttu-id="fe4c7-202">我 `Blog` 的类按约定命名，代码优先假设这将映射到名为的表 `Blogs` 。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-202">My class is named `Blog` and by convention, code first presumes this will map to a table named `Blogs`.</span></span> <span data-ttu-id="fe4c7-203">如果不是这种情况，则可以指定具有属性的表的名称 `Table` 。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-203">If that's not the case you can specify the name of the table with the `Table` attribute.</span></span> <span data-ttu-id="fe4c7-204">例如，批注正在将表名指定为 **InternalBlogs**。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-204">Here for example, the annotation is specifying that the table name is **InternalBlogs**.</span></span>

``` csharp
    [Table("InternalBlogs")]
    public class Blog
```

<span data-ttu-id="fe4c7-205">`Column`在指定映射列的属性的同时，批注更加熟练。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-205">The `Column` annotation is a more adept in specifying the attributes of a mapped column.</span></span> <span data-ttu-id="fe4c7-206">您可以规定名称、数据类型，甚至是列在表中的显示顺序。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-206">You can stipulate a name, data type or even the order in which a column appears in the table.</span></span> <span data-ttu-id="fe4c7-207">下面是属性的示例 `Column` 。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-207">Here is an example of the `Column` attribute.</span></span>

``` csharp
    [Column("BlogDescription", TypeName="ntext")]
    public String Description {get;set;}
```

<span data-ttu-id="fe4c7-208">不要将列的 `TypeName` 属性与 DataType DataAnnotation 混淆。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-208">Don’t confuse Column’s `TypeName` attribute with the DataType DataAnnotation.</span></span> <span data-ttu-id="fe4c7-209">数据类型是一种用于 UI 的批注，Code First 会将其忽略。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-209">DataType is an annotation used for the UI and is ignored by Code First.</span></span>

<span data-ttu-id="fe4c7-210">下面是重新生成表后的表。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-210">Here is the table after it’s been regenerated.</span></span> <span data-ttu-id="fe4c7-211">表名称已更改为 **InternalBlogs** ，并且 `Description` 复杂类型中的列现在是 `BlogDescription` 。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-211">The table name has changed to **InternalBlogs** and `Description` column from the complex type is now `BlogDescription`.</span></span> <span data-ttu-id="fe4c7-212">由于在注释中指定了名称，因此 code first 将不会使用以复杂类型名称开头的列名称。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-212">Because the name was specified in the annotation, code first will not use the convention of starting the column name with the name of the complex type.</span></span>

![博客表和列已重命名](~/ef6/media/jj591583-figure08.png)

 

## <a name="databasegenerated"></a><span data-ttu-id="fe4c7-214">DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="fe4c7-214">DatabaseGenerated</span></span>

<span data-ttu-id="fe4c7-215">重要的数据库功能是具有计算属性的能力。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-215">An important database features is the ability to have computed properties.</span></span> <span data-ttu-id="fe4c7-216">如果要将 Code First 类映射到包含计算列的表，则不希望实体框架尝试更新这些列。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-216">If you're mapping your Code First classes to tables that contain computed columns, you don't want Entity Framework to try to update those columns.</span></span> <span data-ttu-id="fe4c7-217">但是，在插入或更新数据后，您确实需要 EF 从数据库返回这些值。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-217">But you do want EF to return those values from the database after you've inserted or updated data.</span></span> <span data-ttu-id="fe4c7-218">你可以使用 `DatabaseGenerated` 批注来标记类中的这些属性以及 `Computed` 枚举。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-218">You can use the `DatabaseGenerated` annotation to flag those properties in your class along with the `Computed` enum.</span></span> <span data-ttu-id="fe4c7-219">其他枚举为 `None` 和 `Identity` 。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-219">Other enums are `None` and `Identity`.</span></span>

``` csharp
    [DatabaseGenerated(DatabaseGeneratedOption.Computed)]
    public DateTime DateCreated { get; set; }
```

<span data-ttu-id="fe4c7-220">当代码优先生成数据库时，可以使用在字节或时间戳列上生成的数据库，否则，只应在指向现有数据库时使用此数据库，因为 code first 无法确定计算列的公式。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-220">You can use database generated on byte or timestamp columns when code first is generating the database, otherwise you should only use this when pointing to existing databases because code first won't be able to determine the formula for the computed column.</span></span>

<span data-ttu-id="fe4c7-221">如上所述，在默认情况下，作为整数的键属性将成为数据库中的标识键。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-221">You read above that by default, a key property that is an integer will become an identity key in the database.</span></span> <span data-ttu-id="fe4c7-222">这与将设置 `DatabaseGenerated` 为相同 `DatabaseGeneratedOption.Identity` 。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-222">That would be the same as setting `DatabaseGenerated` to `DatabaseGeneratedOption.Identity`.</span></span> <span data-ttu-id="fe4c7-223">如果你不希望它是标识密钥，则可以将值设置为 `DatabaseGeneratedOption.None` 。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-223">If you do not want it to be an identity key, you can set the value to `DatabaseGeneratedOption.None`.</span></span>

 

## <a name="index"></a><span data-ttu-id="fe4c7-224">索引</span><span class="sxs-lookup"><span data-stu-id="fe4c7-224">Index</span></span>

> [!NOTE]
> <span data-ttu-id="fe4c7-225">**Ef 6.1 仅** 往上- `Index` 实体框架6.1 中引入了属性。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-225">**EF6.1 Onwards Only** - The `Index` attribute was introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="fe4c7-226">如果你使用的是早期版本，则本部分中的信息不适用。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-226">If you are using an earlier version the information in this section does not apply.</span></span>

<span data-ttu-id="fe4c7-227">您可以使用 **IndexAttribute**对一个或多个列创建索引。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-227">You can create an index on one or more columns using the **IndexAttribute**.</span></span> <span data-ttu-id="fe4c7-228">将属性添加到一个或多个属性时，将导致 EF 在创建数据库时在数据库中创建相应的索引，或者如果使用 Code First 迁移，则为相应的 **CreateIndex** 调用基架。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-228">Adding the attribute to one or more properties will cause EF to create the corresponding index in the database when it creates the database, or scaffold the corresponding **CreateIndex** calls if you are using Code First Migrations.</span></span>

<span data-ttu-id="fe4c7-229">例如，下面的代码将生成在数据库的表的列上创建的索引 `Rating` `Posts` 。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-229">For example, the following code will result in an index being created on the `Rating` column of the `Posts` table in the database.</span></span>

``` csharp
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }
        [Index]
        public int Rating { get; set; }
        public int BlogId { get; set; }
    }
```

<span data-ttu-id="fe4c7-230">默认情况下，上述示例中的索引将命名为**ix \_ &lt; &gt; 属性名称** (ix \_ 分级) 。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-230">By default, the index will be named **IX\_&lt;property name&gt;** (IX\_Rating in the above example).</span></span> <span data-ttu-id="fe4c7-231">您还可以指定索引的名称。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-231">You can also specify a name for the index though.</span></span> <span data-ttu-id="fe4c7-232">下面的示例指定索引应命名为 `PostRatingIndex` 。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-232">The following example specifies that the index should be named `PostRatingIndex`.</span></span>

``` csharp
    [Index("PostRatingIndex")]
    public int Rating { get; set; }
```

<span data-ttu-id="fe4c7-233">默认情况下，索引是不唯一的，但您可以使用 `IsUnique` 命名参数指定索引应是唯一的。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-233">By default, indexes are non-unique, but you can use the `IsUnique` named parameter to specify that an index should be unique.</span></span> <span data-ttu-id="fe4c7-234">下面的示例对 `User` 的登录名引入唯一索引。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-234">The following example introduces a unique index on a `User`'s login name.</span></span>

``` csharp
    public class User
    {
        public int UserId { get; set; }

        [Index(IsUnique = true)]
        [StringLength(200)]
        public string Username { get; set; }

        public string DisplayName { get; set; }
    }
```

### <a name="multiple-column-indexes"></a><span data-ttu-id="fe4c7-235">多列索引</span><span class="sxs-lookup"><span data-stu-id="fe4c7-235">Multiple-Column Indexes</span></span>

<span data-ttu-id="fe4c7-236">跨多个列的索引通过在给定表的多个索引批注中使用相同的名称来指定。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-236">Indexes that span multiple columns are specified by using the same name in multiple Index annotations for a given table.</span></span> <span data-ttu-id="fe4c7-237">创建多列索引时，需要指定索引中列的顺序。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-237">When you create multi-column indexes, you need to specify an order for the columns in the index.</span></span> <span data-ttu-id="fe4c7-238">例如，下面的代码在上创建一个多列索引 `Rating` ，并将其 `BlogId` 称为 **IX \_ BlogIdAndRating**。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-238">For example, the following code creates a multi-column index on `Rating` and `BlogId` called **IX\_BlogIdAndRating**.</span></span> <span data-ttu-id="fe4c7-239">`BlogId` 是索引中的第一列， `Rating` 是第二列。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-239">`BlogId` is the first column in the index and `Rating` is the second.</span></span>

``` csharp
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }
        [Index("IX_BlogIdAndRating", 2)]
        public int Rating { get; set; }
        [Index("IX_BlogIdAndRating", 1)]
        public int BlogId { get; set; }
    }
```

 

## <a name="relationship-attributes-inverseproperty-and-foreignkey"></a><span data-ttu-id="fe4c7-240">关系属性： InverseProperty 和 ForeignKey</span><span class="sxs-lookup"><span data-stu-id="fe4c7-240">Relationship Attributes: InverseProperty and ForeignKey</span></span>

> [!NOTE]
> <span data-ttu-id="fe4c7-241">本页提供有关使用数据批注设置 Code First 模型中的关系的信息。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-241">This page provides information about setting up relationships in your Code First model using Data Annotations.</span></span> <span data-ttu-id="fe4c7-242">有关 EF 中的关系以及如何使用关系访问和操作数据的一般信息，请参阅 [关系 & 导航属性](xref:ef6/fundamentals/relationships)。 \*</span><span class="sxs-lookup"><span data-stu-id="fe4c7-242">For general information about relationships in EF and how to access and manipulate data using relationships, see [Relationships & Navigation Properties](xref:ef6/fundamentals/relationships).\*</span></span>

<span data-ttu-id="fe4c7-243">Code first 约定将负责您的模型中最常见的关系，但在某些情况下，需要帮助。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-243">Code first convention will take care of the most common relationships in your model, but there are some cases where it needs help.</span></span>

<span data-ttu-id="fe4c7-244">更改类中的键属性的名称会 `Blog` 创建一个与的关系相关的问题 `Post` 。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-244">Changing the name of the key property in the `Blog` class created a problem with its relationship to `Post`.</span></span> 

<span data-ttu-id="fe4c7-245">生成数据库时，代码优先会在 `BlogId` Post 类中看到该属性，并将其识别为与类名称和 **Id**相匹配的约定，作为类的外键 `Blog` 。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-245">When generating the database, code first sees the `BlogId` property in the Post class and recognizes it, by the convention that it matches a class name plus **Id**, as a foreign key to the `Blog` class.</span></span> <span data-ttu-id="fe4c7-246">但 `BlogId` 博客类中没有属性。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-246">But there is no `BlogId` property in the blog class.</span></span> <span data-ttu-id="fe4c7-247">此操作的解决方案是在中创建导航属性 `Post` ，并使用 `ForeignKey` DataAnnotation 帮助代码首先了解如何使用属性) 在两个 (类之间建立关系，以及 `Post.BlogId` 如何在数据库中指定约束。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-247">The solution for this is to create a navigation property in the `Post` and use the `ForeignKey` DataAnnotation to help code first understand how to build the relationship between the two classes (using the `Post.BlogId` property) as well as how to specify constraints in the database.</span></span>

``` csharp
    public class Post
    {
            public int Id { get; set; }
            public string Title { get; set; }
            public DateTime DateCreated { get; set; }
            public string Content { get; set; }
            public int BlogId { get; set; }
            [ForeignKey("BlogId")]
            public Blog Blog { get; set; }
            public ICollection<Comment> Comments { get; set; }
    }
```

<span data-ttu-id="fe4c7-248">数据库中的约束显示与之间的关系 `InternalBlogs.PrimaryTrackingKey` `Posts.BlogId` 。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-248">The constraint in the database shows a relationship between `InternalBlogs.PrimaryTrackingKey` and `Posts.BlogId`.</span></span> 

![InternalBlogs 和 PrimaryTrackingKey 之间的关系。 BlogId](~/ef6/media/jj591583-figure09.png)

<span data-ttu-id="fe4c7-250">`InverseProperty`当类之间存在多个关系时，将使用。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-250">The `InverseProperty` is used when you have multiple relationships between classes.</span></span>

<span data-ttu-id="fe4c7-251">在 `Post` 类中，你可能需要跟踪博客文章的作者，以及编辑者。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-251">In the `Post` class, you may want to keep track of who wrote a blog post as well as who edited it.</span></span> <span data-ttu-id="fe4c7-252">下面是 Post 类的两个新导航属性。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-252">Here are two new navigation properties for the Post class.</span></span>

``` csharp
    public Person CreatedBy { get; set; }
    public Person UpdatedBy { get; set; }
```

<span data-ttu-id="fe4c7-253">还需要添加到 `Person` 这些属性引用的类中。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-253">You’ll also need to add in the `Person` class referenced by these properties.</span></span> <span data-ttu-id="fe4c7-254">`Person`类具有返回到的导航属性 `Post` ，一个用于用户编写的所有张贴内容，一个用于由该用户更新的所有发布。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-254">The `Person` class has navigation properties back to the `Post`, one for all of the posts written by the person and one for all of the posts updated by that person.</span></span>

``` csharp
    public class Person
    {
            public int Id { get; set; }
            public string Name { get; set; }
            public List<Post> PostsWritten { get; set; }
            public List<Post> PostsUpdated { get; set; }
    }
```

<span data-ttu-id="fe4c7-255">Code first 不能自行匹配两个类中的属性。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-255">Code first is not able to match up the properties in the two classes on its own.</span></span> <span data-ttu-id="fe4c7-256">的数据库表 `Posts` 应有一个外键 `CreatedBy` ，而另一个用于用户，另一个用于 `UpdatedBy` 用户，但 code first 将创建四个外键属性： **person \_ id**、 **person \_ Id1**、 **system.createdby \_ id** 和 **UpdatedBy \_ id**。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-256">The database table for `Posts` should have one foreign key for the `CreatedBy` person and one for the `UpdatedBy` person but code first will create four foreign key properties: **Person\_Id**, **Person\_Id1**, **CreatedBy\_Id** and **UpdatedBy\_Id**.</span></span>

![带有额外外键的 post 表](~/ef6/media/jj591583-figure10.png)

<span data-ttu-id="fe4c7-258">若要解决这些问题，可以使用 `InverseProperty` 批注来指定属性的对齐方式。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-258">To fix these problems, you can use the `InverseProperty` annotation to specify the alignment of the properties.</span></span>

``` csharp
    [InverseProperty("CreatedBy")]
    public List<Post> PostsWritten { get; set; }

    [InverseProperty("UpdatedBy")]
    public List<Post> PostsUpdated { get; set; }
```

<span data-ttu-id="fe4c7-259">因为 `PostsWritten` Person 中的属性知道这是指该 `Post` 类型，所以它将与建立关系 `Post.CreatedBy` 。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-259">Because the `PostsWritten` property in Person knows that this refers to the `Post` type, it will build the relationship to `Post.CreatedBy`.</span></span> <span data-ttu-id="fe4c7-260">同样， `PostsUpdated` 将连接到 `Post.UpdatedBy` 。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-260">Similarly, `PostsUpdated` will be connected to `Post.UpdatedBy`.</span></span> <span data-ttu-id="fe4c7-261">和 code first 不会创建额外的外键。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-261">And code first will not create the extra foreign keys.</span></span>

![发送无额外外键的表](~/ef6/media/jj591583-figure11.png)

 

## <a name="summary"></a><span data-ttu-id="fe4c7-263">总结</span><span class="sxs-lookup"><span data-stu-id="fe4c7-263">Summary</span></span>

<span data-ttu-id="fe4c7-264">DataAnnotations 不仅使你能够在代码优先类中描述客户端和服务器端验证，还允许你增强，甚至更正代码优先根据其约定对类进行的假设。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-264">DataAnnotations not only let you describe client and server side validation in your code first classes, but they also allow you to enhance and even correct the assumptions that code first will make about your classes based on its conventions.</span></span> <span data-ttu-id="fe4c7-265">使用 DataAnnotations，不仅可以驱动数据库架构生成，还可以将代码的第一类映射到预先存在的数据库。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-265">With DataAnnotations you can not only drive database schema generation, but you can also map your code first classes to a pre-existing database.</span></span>

<span data-ttu-id="fe4c7-266">尽管它们非常灵活，但请记住，DataAnnotations 仅提供您可以在代码优先类上进行的最常见的配置更改。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-266">While they are very flexible, keep in mind that DataAnnotations provide only the most commonly needed configuration changes you can make on your code first classes.</span></span> <span data-ttu-id="fe4c7-267">若要为某些边缘事例配置类，应查看备用配置机制，Code First 的 "流畅" API。</span><span class="sxs-lookup"><span data-stu-id="fe4c7-267">To configure your classes for some of the edge cases, you should look to the alternate configuration mechanism, Code First’s Fluent API .</span></span>
