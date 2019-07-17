---
title: 第一个数据注释-EF6 的代码
author: divega
ms.date: 10/23/2016
ms.assetid: 80abefbd-23c9-4fce-9cd3-520e5df9856e
ms.openlocfilehash: fcd01aef7303573001460b352f8099b2cc6e224a
ms.sourcegitcommit: e90d6cfa3e96f10b8b5275430759a66a0c714ed1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2019
ms.locfileid: "68286483"
---
# <a name="code-first-data-annotations"></a><span data-ttu-id="823d5-102">Code First 数据注释</span><span class="sxs-lookup"><span data-stu-id="823d5-102">Code First Data Annotations</span></span>
> [!NOTE]
> <span data-ttu-id="823d5-103">**EF4.1 及更高版本仅**的功能，Api，Entity Framework 4.1 中引入了此页所述的等。</span><span class="sxs-lookup"><span data-stu-id="823d5-103">**EF4.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 4.1.</span></span> <span data-ttu-id="823d5-104">如果您使用的是早期版本，则不适用的部分或所有这些信息。</span><span class="sxs-lookup"><span data-stu-id="823d5-104">If you are using an earlier version, some or all of this information does not apply.</span></span>

<span data-ttu-id="823d5-105">此页上的内容是从作者： Julie Lerman 最初编写一篇文章 (\<http://thedatafarm.com>) 。</span><span class="sxs-lookup"><span data-stu-id="823d5-105">The content on this page is adapted from an article originally written by Julie Lerman (\<http://thedatafarm.com>).</span></span>

<span data-ttu-id="823d5-106">实体框架 Code First 允许您使用您自己的域类来表示 EF 依赖于执行查询，该模型更改跟踪和更新功能。</span><span class="sxs-lookup"><span data-stu-id="823d5-106">Entity Framework Code First allows you to use your own domain classes to represent the model that EF relies on to perform querying, change tracking, and updating functions.</span></span> <span data-ttu-id="823d5-107">代码首先将利用一种编程模式称为惯例优先于配置。</span><span class="sxs-lookup"><span data-stu-id="823d5-107">Code First leverages a programming pattern referred to as 'convention over configuration.'</span></span> <span data-ttu-id="823d5-108">代码首先将假定您的类遵循的约定的实体框架，以及在这种情况下，将自动解决如何执行它的作业。</span><span class="sxs-lookup"><span data-stu-id="823d5-108">Code First will assume that your classes follow the conventions of Entity Framework, and in that case, will automatically work out how to perform it's job.</span></span> <span data-ttu-id="823d5-109">但是，如果您的类不遵循这些约定，必须将配置添加到您的类以提供必要的信息，EF 的功能。</span><span class="sxs-lookup"><span data-stu-id="823d5-109">However, if your classes do not follow those conventions, you have the ability to add configurations to your classes to provide EF with the requisite information.</span></span>

<span data-ttu-id="823d5-110">代码首先提供了两种方法将这些配置添加到您的类。</span><span class="sxs-lookup"><span data-stu-id="823d5-110">Code First gives you two ways to add these configurations to your classes.</span></span> <span data-ttu-id="823d5-111">一种使用简单的属性名为 DataAnnotations，并且第二个使用 Code First Fluent API，后者为您提供一种在代码中以强制方式，描述配置方法。</span><span class="sxs-lookup"><span data-stu-id="823d5-111">One is using simple attributes called DataAnnotations, and the second is using Code First’s Fluent API, which provides you with a way to describe configurations imperatively, in code.</span></span>

<span data-ttu-id="823d5-112">本文将重点介绍使用 DataAnnotations （System.ComponentModel.DataAnnotations 命名空间中） 来配置你的类，突出显示的最常见所需的配置。</span><span class="sxs-lookup"><span data-stu-id="823d5-112">This article will focus on using DataAnnotations (in the System.ComponentModel.DataAnnotations namespace) to configure your classes – highlighting the most commonly needed configurations.</span></span> <span data-ttu-id="823d5-113">DataAnnotations 还能够理解的大量的.NET 应用程序，如 ASP.NET MVC 允许这些应用程序可以利用相同的注释进行客户端验证。</span><span class="sxs-lookup"><span data-stu-id="823d5-113">DataAnnotations are also understood by a number of .NET applications, such as ASP.NET MVC which allows these applications to leverage the same annotations for client-side validations.</span></span>


## <a name="the-model"></a><span data-ttu-id="823d5-114">模型</span><span class="sxs-lookup"><span data-stu-id="823d5-114">The model</span></span>

<span data-ttu-id="823d5-115">我将演示代码使用类的一个简单的对第一个 DataAnnotations:博客和文章。</span><span class="sxs-lookup"><span data-stu-id="823d5-115">I’ll demonstrate Code First DataAnnotations with a simple pair of classes: Blog and Post.</span></span>

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

<span data-ttu-id="823d5-116">因为它们是在博客和文章类方便地遵循代码的第一个约定并需要启用 EF 兼容性的调整。</span><span class="sxs-lookup"><span data-stu-id="823d5-116">As they are, the Blog and Post classes conveniently follow code first convention and require no tweaks to enable EF compatability.</span></span> <span data-ttu-id="823d5-117">但是，您可以使用批注的类和数据库映射到的有关向 EF 提供的详细信息。</span><span class="sxs-lookup"><span data-stu-id="823d5-117">However, you can also use the annotations to provide more information to EF about the classes and the database to which they map.</span></span>

 

## <a name="key"></a><span data-ttu-id="823d5-118">Key</span><span class="sxs-lookup"><span data-stu-id="823d5-118">Key</span></span>

<span data-ttu-id="823d5-119">Entity Framework 依赖于具有一个密钥值，用于跟踪的实体的每个实体。</span><span class="sxs-lookup"><span data-stu-id="823d5-119">Entity Framework relies on every entity having a key value that is used for entity tracking.</span></span> <span data-ttu-id="823d5-120">一种约定的 Code First 是隐式键属性;第一次代码将查找名为"Id"或类名称和"Id"，如"BlogId"的组合的属性。</span><span class="sxs-lookup"><span data-stu-id="823d5-120">One convention of Code First is implicit key properties; Code First will look for a property named “Id”, or a combination of class name and “Id”, such as “BlogId”.</span></span> <span data-ttu-id="823d5-121">此属性将映射到数据库中的主键列。</span><span class="sxs-lookup"><span data-stu-id="823d5-121">This property will map to a primary key column in the database.</span></span>

<span data-ttu-id="823d5-122">博客和文章类都遵循此约定。</span><span class="sxs-lookup"><span data-stu-id="823d5-122">The Blog and Post classes both follow this convention.</span></span> <span data-ttu-id="823d5-123">如果他们不乐意这样？</span><span class="sxs-lookup"><span data-stu-id="823d5-123">What if they didn’t?</span></span> <span data-ttu-id="823d5-124">如果博客使用名称*PrimaryTrackingKey*相反，或甚至*foo*？</span><span class="sxs-lookup"><span data-stu-id="823d5-124">What if Blog used the name *PrimaryTrackingKey* instead, or even *foo*?</span></span> <span data-ttu-id="823d5-125">如果代码第一次未找到匹配此约定属性它将引发异常，由于实体框架的需求，您必须具有键属性。</span><span class="sxs-lookup"><span data-stu-id="823d5-125">If code first does not find a property that matches this convention it will throw an exception because of Entity Framework’s requirement that you must have a key property.</span></span> <span data-ttu-id="823d5-126">关键批注用于指定哪些属性是要使用的 entitykey。</span><span class="sxs-lookup"><span data-stu-id="823d5-126">You can use the key annotation to specify which property is to be used as the EntityKey.</span></span>

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

<span data-ttu-id="823d5-127">如果您是使用 code first 是数据库生成功能、 博客表具有名为 PrimaryTrackingKey，默认情况下也定义为标识主键列。</span><span class="sxs-lookup"><span data-stu-id="823d5-127">If you are using code first’s database generation feature, the Blog table will have a primary key column named PrimaryTrackingKey, which is also defined as Identity by default.</span></span>

![博客具有主键的表](~/ef6/media/jj591583-figure01.png)

### <a name="composite-keys"></a><span data-ttu-id="823d5-129">组合键</span><span class="sxs-lookup"><span data-stu-id="823d5-129">Composite keys</span></span>

<span data-ttu-id="823d5-130">实体框架支持的复合键-主键由多个属性组成。</span><span class="sxs-lookup"><span data-stu-id="823d5-130">Entity Framework supports composite keys - primary keys that consist of more than one property.</span></span> <span data-ttu-id="823d5-131">例如，你可以其主键为 PassportNumber 和 IssuingCountry 的组合的 Passport 类。</span><span class="sxs-lookup"><span data-stu-id="823d5-131">For example, you could have a Passport class whose primary key is a combination of PassportNumber and IssuingCountry.</span></span>

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

<span data-ttu-id="823d5-132">尝试在 EF 模型中使用上述的类将导致`InvalidOperationException`:</span><span class="sxs-lookup"><span data-stu-id="823d5-132">Attempting to use the above class in your EF model would result in an `InvalidOperationException`:</span></span>

<span data-ttu-id="823d5-133">*无法确定复合主键排序类型 Passport。使用 ColumnAttribute 或 HasKey 方法指定为复合主键的订单。*</span><span class="sxs-lookup"><span data-stu-id="823d5-133">*Unable to determine composite primary key ordering for type 'Passport'. Use the ColumnAttribute or the HasKey method to specify an order for composite primary keys.*</span></span>

<span data-ttu-id="823d5-134">若要使用的复合键，实体框架要求定义键属性的顺序。</span><span class="sxs-lookup"><span data-stu-id="823d5-134">In order to use composite keys, Entity Framework requires you to define an order for the key properties.</span></span> <span data-ttu-id="823d5-135">可以使用列批注以指定的顺序来执行此操作。</span><span class="sxs-lookup"><span data-stu-id="823d5-135">You can do this by using the Column annotation to specify an order.</span></span>

>[!NOTE]
> <span data-ttu-id="823d5-136">将顺序值是相对 （而非基于索引） 以便可以使用任何值。</span><span class="sxs-lookup"><span data-stu-id="823d5-136">The order value is relative (rather than index based) so any values can be used.</span></span> <span data-ttu-id="823d5-137">例如，100 到 200 个将代替 1 和 2 可接受。</span><span class="sxs-lookup"><span data-stu-id="823d5-137">For example, 100 and 200 would be acceptable in place of 1 and 2.</span></span>

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

<span data-ttu-id="823d5-138">如果你有具有复合外键的实体，则必须指定相同的列排序用于相应的主键属性。</span><span class="sxs-lookup"><span data-stu-id="823d5-138">If you have entities with composite foreign keys, then you must specify the same column ordering that you used for the corresponding primary key properties.</span></span>

<span data-ttu-id="823d5-139">仅的相对顺序中的外键属性必须是相同的确切的值分配给**顺序**不需要匹配。</span><span class="sxs-lookup"><span data-stu-id="823d5-139">Only the relative ordering within the foreign key properties needs to be the same, the exact values assigned to **Order** do not need to match.</span></span> <span data-ttu-id="823d5-140">例如，在以下类中，3 和 4 可用来代替 1 和 2。</span><span class="sxs-lookup"><span data-stu-id="823d5-140">For example, in the following class, 3 and 4 could be used in place of 1 and 2.</span></span>

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

## <a name="required"></a><span data-ttu-id="823d5-141">必填</span><span class="sxs-lookup"><span data-stu-id="823d5-141">Required</span></span>

<span data-ttu-id="823d5-142">需要的批注告知 EF 特定的属性是必需。</span><span class="sxs-lookup"><span data-stu-id="823d5-142">The Required annotation tells EF that a particular property is required.</span></span>

<span data-ttu-id="823d5-143">添加所需的 Title 属性将强制 EF （和 MVC） 以确保该属性中包含的数据。</span><span class="sxs-lookup"><span data-stu-id="823d5-143">Adding Required to the Title property will force EF (and MVC) to ensure that the property has data in it.</span></span>

``` csharp
    [Required]
    public string Title { get; set; }
```

<span data-ttu-id="823d5-144">无需额外的代码或标记在应用程序中的更改，与 MVC 应用程序将执行客户端验证，甚至动态生成使用的属性和批注名称的消息。</span><span class="sxs-lookup"><span data-stu-id="823d5-144">With no additional code or markup changes in the application, an MVC application will perform client side validation, even dynamically building a message using the property and annotation names.</span></span>

![创建具有标题页是所需的错误](~/ef6/media/jj591583-figure02.png)

<span data-ttu-id="823d5-146">所需的属性也会影响生成的数据库，从而映射的属性不可为 null。</span><span class="sxs-lookup"><span data-stu-id="823d5-146">The Required attribute will also affect the generated database by making the mapped property non-nullable.</span></span> <span data-ttu-id="823d5-147">请注意，标题字段已更改为"not null"。</span><span class="sxs-lookup"><span data-stu-id="823d5-147">Notice that the Title field has changed to “not null”.</span></span>

>[!NOTE]
> <span data-ttu-id="823d5-148">在某些情况下它不可能要为不可为 null，即使该属性是必需的数据库中的列。</span><span class="sxs-lookup"><span data-stu-id="823d5-148">In some cases it may not be possible for the column in the database to be non-nullable even though the property is required.</span></span> <span data-ttu-id="823d5-149">例如，当对多个类型使用 TPH 继承策略数据存储在单个表。</span><span class="sxs-lookup"><span data-stu-id="823d5-149">For example, when using a TPH inheritance strategy data for multiple types is stored in a single table.</span></span> <span data-ttu-id="823d5-150">如果派生的类型包括必需的属性列不能成为不可为 null 因为层次结构中的不是所有类型都将都具有此属性。</span><span class="sxs-lookup"><span data-stu-id="823d5-150">If a derived type includes a required property the column cannot be made non-nullable since not all types in the hierarchy will have this property.</span></span>

 

![Blogs 表](~/ef6/media/jj591583-figure03.png)

 

## <a name="maxlength-and-minlength"></a><span data-ttu-id="823d5-152">MaxLength 和 MinLength</span><span class="sxs-lookup"><span data-stu-id="823d5-152">MaxLength and MinLength</span></span>

<span data-ttu-id="823d5-153">MaxLength 和 MinLength 属性，可以指定其他属性验证，像您那样使用所需。</span><span class="sxs-lookup"><span data-stu-id="823d5-153">The MaxLength and MinLength attributes allow you to specify additional property validations, just as you did with Required.</span></span>

<span data-ttu-id="823d5-154">下面是具有长度要求 BloggerName。</span><span class="sxs-lookup"><span data-stu-id="823d5-154">Here is the BloggerName with length requirements.</span></span> <span data-ttu-id="823d5-155">该示例还演示如何组合属性。</span><span class="sxs-lookup"><span data-stu-id="823d5-155">The example also demonstrates how to combine attributes.</span></span>

``` csharp
    [MaxLength(10),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="823d5-156">MaxLength 批注会影响数据库的属性的长度设置为 10。</span><span class="sxs-lookup"><span data-stu-id="823d5-156">The MaxLength annotation will impact the database by setting the property’s length to 10.</span></span>

![Blogs 表 BloggerName 列上显示的最大长度](~/ef6/media/jj591583-figure04.png)

<span data-ttu-id="823d5-158">客户端的批注 MVC 和 EF 4.1 服务器端批注都将遵循此验证，再次动态生成一条错误消息："字段 BloggerName 具有最大长度为"10"的字符串或数组类型必须是"。该消息是有点长。</span><span class="sxs-lookup"><span data-stu-id="823d5-158">MVC client-side annotation and EF 4.1 server-side annotation will both honor this validation, again dynamically building an error message: “The field BloggerName must be a string or array type with a maximum length of '10'.” That message is a little long.</span></span> <span data-ttu-id="823d5-159">很多批注，可以使用 ErrorMessage 属性指定一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="823d5-159">Many annotations let you specify an error message with the ErrorMessage attribute.</span></span>

``` csharp
    [MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="823d5-160">此外可以在所需的批注中指定错误消息。</span><span class="sxs-lookup"><span data-stu-id="823d5-160">You can also specify ErrorMessage in the Required annotation.</span></span>

![创建具有自定义错误消息页](~/ef6/media/jj591583-figure05.png)

 

## <a name="notmapped"></a><span data-ttu-id="823d5-162">NotMapped</span><span class="sxs-lookup"><span data-stu-id="823d5-162">NotMapped</span></span>

<span data-ttu-id="823d5-163">第一个代码约定决定了是受支持的数据类型的每个属性表示为数据库中。</span><span class="sxs-lookup"><span data-stu-id="823d5-163">Code first convention dictates that every property that is of a supported data type is represented in the database.</span></span> <span data-ttu-id="823d5-164">但这并非总是这种情况在您的应用程序。</span><span class="sxs-lookup"><span data-stu-id="823d5-164">But this isn’t always the case in your applications.</span></span> <span data-ttu-id="823d5-165">例如，可能博客类，用于创建基于字段的标题和 BloggerName 代码中有属性。</span><span class="sxs-lookup"><span data-stu-id="823d5-165">For example you might have a property in the Blog class that creates a code based on the Title and BloggerName fields.</span></span> <span data-ttu-id="823d5-166">该属性可以动态创建实体并且不需要存储。</span><span class="sxs-lookup"><span data-stu-id="823d5-166">That property can be created dynamically and does not need to be stored.</span></span> <span data-ttu-id="823d5-167">可以将标记不会映射到此 BlogCode 属性如 NotMapped 批注与数据库的任何属性。</span><span class="sxs-lookup"><span data-stu-id="823d5-167">You can mark any properties that do not map to the database with the NotMapped annotation such as this BlogCode property.</span></span>

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

 

## <a name="complextype"></a><span data-ttu-id="823d5-168">ComplexType</span><span class="sxs-lookup"><span data-stu-id="823d5-168">ComplexType</span></span>

<span data-ttu-id="823d5-169">不常见对域实体跨一组类的描述，然后分层这些类来描述完整实体。</span><span class="sxs-lookup"><span data-stu-id="823d5-169">It’s not uncommon to describe your domain entities across a set of classes and then layer those classes to describe a complete entity.</span></span> <span data-ttu-id="823d5-170">例如，您可以添加到您的模型名 BlogDetails 的类。</span><span class="sxs-lookup"><span data-stu-id="823d5-170">For example, you may add a class called BlogDetails to your model.</span></span>

``` csharp
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

<span data-ttu-id="823d5-171">请注意 BlogDetails 不具有任何类型的键属性。</span><span class="sxs-lookup"><span data-stu-id="823d5-171">Notice that BlogDetails does not have any type of key property.</span></span> <span data-ttu-id="823d5-172">在域驱动设计中，BlogDetails 称为值对象。</span><span class="sxs-lookup"><span data-stu-id="823d5-172">In domain driven design, BlogDetails is referred to as a value object.</span></span> <span data-ttu-id="823d5-173">实体框架将值对象称为复杂类型。</span><span class="sxs-lookup"><span data-stu-id="823d5-173">Entity Framework refers to value objects as complex types.</span></span><span data-ttu-id="823d5-174">  不能自行跟踪复杂类型。</span><span class="sxs-lookup"><span data-stu-id="823d5-174">  Complex types cannot be tracked on their own.</span></span>

<span data-ttu-id="823d5-175">但是作为博客类，它将作为博客对象的一部分进行跟踪的 BlogDetails 中的属性。</span><span class="sxs-lookup"><span data-stu-id="823d5-175">However as a property in the Blog class, BlogDetails it will be tracked as part of a Blog object.</span></span> <span data-ttu-id="823d5-176">为了使代码首先将这种情况，你必须将 BlogDetails 类标记为复杂类型。</span><span class="sxs-lookup"><span data-stu-id="823d5-176">In order for code first to recognize this, you must mark the BlogDetails class as a ComplexType.</span></span>

``` csharp
    [ComplexType]
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

<span data-ttu-id="823d5-177">现在可以在博客类来表示该博客 BlogDetails 中添加一个属性。</span><span class="sxs-lookup"><span data-stu-id="823d5-177">Now you can add a property in the Blog class to represent the BlogDetails for that blog.</span></span>

``` csharp
        public BlogDetails BlogDetail { get; set; }
```

<span data-ttu-id="823d5-178">在数据库中，博客表将包含的所有博客包括其 BlogDetail 属性中包含的属性的属性。</span><span class="sxs-lookup"><span data-stu-id="823d5-178">In the database, the Blog table will contain all of the properties of the blog including the properties contained in its BlogDetail property.</span></span> <span data-ttu-id="823d5-179">默认情况下，每个前面带有复杂类型，BlogDetail 的名称。</span><span class="sxs-lookup"><span data-stu-id="823d5-179">By default, each one is preceded with the name of the complex type, BlogDetail.</span></span>

![具有复杂类型的网络日志表](~/ef6/media/jj591583-figure06.png)


## <a name="concurrencycheck"></a><span data-ttu-id="823d5-181">ConcurrencyCheck</span><span class="sxs-lookup"><span data-stu-id="823d5-181">ConcurrencyCheck</span></span>

<span data-ttu-id="823d5-182">ConcurrencyCheck 批注可以标记一个或多个要在进行并发检查数据库中，当用户编辑或删除的实体的属性。</span><span class="sxs-lookup"><span data-stu-id="823d5-182">The ConcurrencyCheck annotation allows you to flag one or more properties to be used for concurrency checking in the database when a user edits or deletes an entity.</span></span> <span data-ttu-id="823d5-183">如果您一直使用 EF 设计器，这会将与将属性的 ConcurrencyMode 设置为 Fixed。</span><span class="sxs-lookup"><span data-stu-id="823d5-183">If you've been working with the EF Designer, this aligns with setting a property's ConcurrencyMode to Fixed.</span></span>

<span data-ttu-id="823d5-184">让我们了解 ConcurrencyCheck 通过将其添加到 BloggerName 属性的工作原理。</span><span class="sxs-lookup"><span data-stu-id="823d5-184">Let’s see how ConcurrencyCheck works by adding it to the BloggerName property.</span></span>

``` csharp
    [ConcurrencyCheck, MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="823d5-185">当调用 SaveChanges 时，由于 ConcurrencyCheck 批注 BloggerName 字段中，将更新中使用该属性的原始值。</span><span class="sxs-lookup"><span data-stu-id="823d5-185">When SaveChanges is called, because of the ConcurrencyCheck annotation on the BloggerName field, the original value of that property will be used in the update.</span></span> <span data-ttu-id="823d5-186">该命令将尝试查找正确的行通过筛选不仅上的密钥值，还在 BloggerName 的原始值。</span><span class="sxs-lookup"><span data-stu-id="823d5-186">The command will attempt to locate the correct row by filtering not only on the key value but also on the original value of BloggerName.</span></span><span data-ttu-id="823d5-187">  以下是 UPDATE 命令发送到数据库，您可以看到该命令将更新包含 PrimaryTrackingKey 的行的关键部分为 1，该博客从数据库中检索时的原始值的"Julie"BloggerName。</span><span class="sxs-lookup"><span data-stu-id="823d5-187">  Here are the critical parts of the UPDATE command sent to the database, where you can see the command will update the row that has a PrimaryTrackingKey is 1 and a BloggerName of “Julie” which was the original value when that blog was retrieved from the database.</span></span>

``` SQL
    where (([PrimaryTrackingKey] = @4) and ([BloggerName] = @5))
    @4=1,@5=N'Julie'
```

<span data-ttu-id="823d5-188">如果有人已更改该博客的博主名称，请在此期间，此更新将失败，则会将需要处理 DbUpdateConcurrencyException。</span><span class="sxs-lookup"><span data-stu-id="823d5-188">If someone has changed the blogger name for that blog in the meantime, this update will fail and you’ll get a DbUpdateConcurrencyException that you'll need to handle.</span></span>

 

## <a name="timestamp"></a><span data-ttu-id="823d5-189">TimeStamp</span><span class="sxs-lookup"><span data-stu-id="823d5-189">TimeStamp</span></span>

<span data-ttu-id="823d5-190">它是更常见的是进行并发检查中使用行版本或时间戳字段。</span><span class="sxs-lookup"><span data-stu-id="823d5-190">It's more common to use rowversion or timestamp fields for concurrency checking.</span></span> <span data-ttu-id="823d5-191">但是，而不是使用 ConcurrencyCheck 批注，您可以使用更具体的时间戳批注的属性类型为字节数组。</span><span class="sxs-lookup"><span data-stu-id="823d5-191">But rather than using the ConcurrencyCheck annotation, you can use the more specific TimeStamp annotation as long as the type of the property is byte array.</span></span> <span data-ttu-id="823d5-192">代码首先将相同的处理时间戳属性为 ConcurrencyCheck 属性，但它还将确保代码首先生成的数据库字段是不可为 null。</span><span class="sxs-lookup"><span data-stu-id="823d5-192">Code first will treat Timestamp properties the same as ConcurrencyCheck properties, but it will also ensure that the database field that code first generates is non-nullable.</span></span> <span data-ttu-id="823d5-193">仅可以在给定类中有一个时间戳属性。</span><span class="sxs-lookup"><span data-stu-id="823d5-193">You can only have one timestamp property in a given class.</span></span>

<span data-ttu-id="823d5-194">将以下属性添加到博客类：</span><span class="sxs-lookup"><span data-stu-id="823d5-194">Adding the following property to the Blog class:</span></span>

``` csharp
    [Timestamp]
    public Byte[] TimeStamp { get; set; }
```

<span data-ttu-id="823d5-195">在代码中首次在数据库表中创建不可以为 null 的时间戳列的结果。</span><span class="sxs-lookup"><span data-stu-id="823d5-195">results in code first creating a non-nullable timestamp column in the database table.</span></span>

![使用时间戳列的 Blogs 表](~/ef6/media/jj591583-figure07.png)

 

## <a name="table-and-column"></a><span data-ttu-id="823d5-197">表和列</span><span class="sxs-lookup"><span data-stu-id="823d5-197">Table and Column</span></span>

<span data-ttu-id="823d5-198">如果您让代码优先在创建数据库，可能想要更改的表和列创建的名称。</span><span class="sxs-lookup"><span data-stu-id="823d5-198">If you are letting Code First create the database, you may want to change the name of the tables and columns it is creating.</span></span> <span data-ttu-id="823d5-199">您还可以使用 Code First 与现有数据库。</span><span class="sxs-lookup"><span data-stu-id="823d5-199">You can also use Code First with an existing database.</span></span> <span data-ttu-id="823d5-200">但它并不总是类和你的域中的属性的名称与表和数据库中的列的名称相匹配的情况。</span><span class="sxs-lookup"><span data-stu-id="823d5-200">But it's not always the case that the names of the classes and properties in your domain match the names of the tables and columns in your database.</span></span>

<span data-ttu-id="823d5-201">我的类名为博客，并按照约定，代码首先假定这会映射到名为博客的表。</span><span class="sxs-lookup"><span data-stu-id="823d5-201">My class is named Blog and by convention, code first presumes this will map to a table named Blogs.</span></span> <span data-ttu-id="823d5-202">如果不是这种情况可以使用表属性中指定的表的名称。</span><span class="sxs-lookup"><span data-stu-id="823d5-202">If that's not the case you can specify the name of the table with the Table attribute.</span></span> <span data-ttu-id="823d5-203">此处，批注指定表名 InternalBlogs。</span><span class="sxs-lookup"><span data-stu-id="823d5-203">Here for example, the annotation is specifying that the table name is InternalBlogs.</span></span>

``` csharp
    [Table("InternalBlogs")]
    public class Blog
```

<span data-ttu-id="823d5-204">列批注是详细擅长中指定的映射列的特性。</span><span class="sxs-lookup"><span data-stu-id="823d5-204">The Column annotation is a more adept in specifying the attributes of a mapped column.</span></span> <span data-ttu-id="823d5-205">您可以规定名称、 数据类型或甚至一列的表中显示的顺序。</span><span class="sxs-lookup"><span data-stu-id="823d5-205">You can stipulate a name, data type or even the order in which a column appears in the table.</span></span> <span data-ttu-id="823d5-206">下面是列属性的一个示例。</span><span class="sxs-lookup"><span data-stu-id="823d5-206">Here is an example of the Column attribute.</span></span>

``` csharp
    [Column("BlogDescription", TypeName="ntext")]
    public String Description {get;set;}
```

<span data-ttu-id="823d5-207">不要混淆列的数据类型 DataAnnotation TypeName 属性。</span><span class="sxs-lookup"><span data-stu-id="823d5-207">Don’t confuse Column’s TypeName attribute with the DataType DataAnnotation.</span></span> <span data-ttu-id="823d5-208">数据类型是 UI 使用一条注释，并忽略由 Code First。</span><span class="sxs-lookup"><span data-stu-id="823d5-208">DataType is an annotation used for the UI and is ignored by Code First.</span></span>

<span data-ttu-id="823d5-209">下面是表后已重新生成。</span><span class="sxs-lookup"><span data-stu-id="823d5-209">Here is the table after it’s been regenerated.</span></span> <span data-ttu-id="823d5-210">表名已更改为 InternalBlogs，从复杂类型说明列现在是 BlogDescription。</span><span class="sxs-lookup"><span data-stu-id="823d5-210">The table name has changed to InternalBlogs and Description column from the complex type is now BlogDescription.</span></span> <span data-ttu-id="823d5-211">因为在批注中指定了名称，但代码首先将不使用的复杂类型的名称开头的列名称的约定。</span><span class="sxs-lookup"><span data-stu-id="823d5-211">Because the name was specified in the annotation, code first will not use the convention of starting the column name with the name of the complex type.</span></span>

![Blogs 表和列重命名](~/ef6/media/jj591583-figure08.png)

 

## <a name="databasegenerated"></a><span data-ttu-id="823d5-213">DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="823d5-213">DatabaseGenerated</span></span>

<span data-ttu-id="823d5-214">重要的数据库功能是能够具有计算属性。</span><span class="sxs-lookup"><span data-stu-id="823d5-214">An important database features is the ability to have computed properties.</span></span> <span data-ttu-id="823d5-215">如果您打算映射代码优先类到包含的表的计算列，您不希望实体框架可以尝试更新这些列。</span><span class="sxs-lookup"><span data-stu-id="823d5-215">If you're mapping your Code First classes to tables that contain computed columns, you don't want Entity Framework to try to update those columns.</span></span> <span data-ttu-id="823d5-216">但您希望 EF 从数据库中返回这些值后已插入或更新数据。</span><span class="sxs-lookup"><span data-stu-id="823d5-216">But you do want EF to return those values from the database after you've inserted or updated data.</span></span> <span data-ttu-id="823d5-217">DatabaseGenerated 批注可用于计算枚举以及在类中标记这些属性。</span><span class="sxs-lookup"><span data-stu-id="823d5-217">You can use the DatabaseGenerated annotation to flag those properties in your class along with the Computed enum.</span></span> <span data-ttu-id="823d5-218">其他枚举都是无和标识。</span><span class="sxs-lookup"><span data-stu-id="823d5-218">Other enums are None and Identity.</span></span>

``` csharp
    [DatabaseGenerated(DatabaseGeneratedOption.Computed)]
    public DateTime DateCreated { get; set; }
```

<span data-ttu-id="823d5-219">可以使用代码首先生成数据库时生成字节或时间戳列上的数据库，否则您才应使用此时指向现有数据库，因为代码首先将无法确定的公式计算所得的列。</span><span class="sxs-lookup"><span data-stu-id="823d5-219">You can use database generated on byte or timestamp columns when code first is generating the database, otherwise you should only use this when pointing to existing databases because code first won't be able to determine the formula for the computed column.</span></span>

<span data-ttu-id="823d5-220">默认情况下，读取上面是一个整数键属性将成为在数据库中的标识键。</span><span class="sxs-lookup"><span data-stu-id="823d5-220">You read above that by default, a key property that is an integer will become an identity key in the database.</span></span> <span data-ttu-id="823d5-221">这将是与将 DatabaseGenerated 设为 DatabaseGeneratedOption.Identity 相同。</span><span class="sxs-lookup"><span data-stu-id="823d5-221">That would be the same as setting DatabaseGenerated to DatabaseGeneratedOption.Identity.</span></span> <span data-ttu-id="823d5-222">如果不希望其成为标识键，您可以将值设置为 DatabaseGeneratedOption.None。</span><span class="sxs-lookup"><span data-stu-id="823d5-222">If you do not want it to be an identity key, you can set the value to DatabaseGeneratedOption.None.</span></span>

 

## <a name="index"></a><span data-ttu-id="823d5-223">索引</span><span class="sxs-lookup"><span data-stu-id="823d5-223">Index</span></span>

> [!NOTE]
> <span data-ttu-id="823d5-224">**EF6.1 及更高版本仅**-Entity Framework 6.1 中引入了索引属性。</span><span class="sxs-lookup"><span data-stu-id="823d5-224">**EF6.1 Onwards Only** - The Index attribute was introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="823d5-225">如果您使用的是早期版本不适用于此部分中的信息。</span><span class="sxs-lookup"><span data-stu-id="823d5-225">If you are using an earlier version the information in this section does not apply.</span></span>

<span data-ttu-id="823d5-226">可以使用的一个或多个列创建索引**IndexAttribute**。</span><span class="sxs-lookup"><span data-stu-id="823d5-226">You can create an index on one or more columns using the **IndexAttribute**.</span></span> <span data-ttu-id="823d5-227">将特性添加到一个或多个属性将导致 EF 在数据库中创建相应的索引时自动创建该数据库，或创建相应的基架**CreateIndex**调用，如果使用 Code First 迁移。</span><span class="sxs-lookup"><span data-stu-id="823d5-227">Adding the attribute to one or more properties will cause EF to create the corresponding index in the database when it creates the database, or scaffold the corresponding **CreateIndex** calls if you are using Code First Migrations.</span></span>

<span data-ttu-id="823d5-228">例如，下面的代码将导致在创建索引**评级**的列**帖子**数据库表中的。</span><span class="sxs-lookup"><span data-stu-id="823d5-228">For example, the following code will result in an index being created on the **Rating** column of the **Posts** table in the database.</span></span>

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

<span data-ttu-id="823d5-229">默认情况下，将名为索引**IX\_&lt;属性名称&gt;** (IX\_评级在上面的示例)。</span><span class="sxs-lookup"><span data-stu-id="823d5-229">By default, the index will be named **IX\_&lt;property name&gt;** (IX\_Rating in the above example).</span></span> <span data-ttu-id="823d5-230">但还可以指定索引的名称。</span><span class="sxs-lookup"><span data-stu-id="823d5-230">You can also specify a name for the index though.</span></span> <span data-ttu-id="823d5-231">下面的示例指定该索引，应将命名为**PostRatingIndex**。</span><span class="sxs-lookup"><span data-stu-id="823d5-231">The following example specifies that the index should be named **PostRatingIndex**.</span></span>

``` csharp
    [Index("PostRatingIndex")]
    public int Rating { get; set; }
```

<span data-ttu-id="823d5-232">默认情况下，索引是非唯一的但你可以使用**IsUnique**名为参数来指定应该唯一索引。</span><span class="sxs-lookup"><span data-stu-id="823d5-232">By default, indexes are non-unique, but you can use the **IsUnique** named parameter to specify that an index should be unique.</span></span> <span data-ttu-id="823d5-233">下面的示例上引入了唯一索引**用户**的登录名。</span><span class="sxs-lookup"><span data-stu-id="823d5-233">The following example introduces a unique index on a **User**'s login name.</span></span>

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

### <a name="multiple-column-indexes"></a><span data-ttu-id="823d5-234">多列索引</span><span class="sxs-lookup"><span data-stu-id="823d5-234">Multiple-Column Indexes</span></span>

<span data-ttu-id="823d5-235">跨多个列的索引指定为给定的表在多个索引批注中使用相同的名称。</span><span class="sxs-lookup"><span data-stu-id="823d5-235">Indexes that span multiple columns are specified by using the same name in multiple Index annotations for a given table.</span></span> <span data-ttu-id="823d5-236">创建多列索引时，需要在索引中指定列的顺序。</span><span class="sxs-lookup"><span data-stu-id="823d5-236">When you create multi-column indexes, you need to specify an order for the columns in the index.</span></span> <span data-ttu-id="823d5-237">例如，以下代码将创建在多列索引**评级**并**BlogId**调用**IX\_BlogIdAndRating**。</span><span class="sxs-lookup"><span data-stu-id="823d5-237">For example, the following code creates a multi-column index on **Rating** and **BlogId** called **IX\_BlogIdAndRating**.</span></span> <span data-ttu-id="823d5-238">**BlogId**是在索引中的第一列和**评级**是第二个。</span><span class="sxs-lookup"><span data-stu-id="823d5-238">**BlogId** is the first column in the index and **Rating** is the second.</span></span>

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

 

## <a name="relationship-attributes-inverseproperty-and-foreignkey"></a><span data-ttu-id="823d5-239">关系的属性：InverseProperty 和 ForeignKey</span><span class="sxs-lookup"><span data-stu-id="823d5-239">Relationship Attributes: InverseProperty and ForeignKey</span></span>

> [!NOTE]
> <span data-ttu-id="823d5-240">此页提供了有关设置关系中使用数据注释在 Code First 模型的信息。</span><span class="sxs-lookup"><span data-stu-id="823d5-240">This page provides information about setting up relationships in your Code First model using Data Annotations.</span></span> <span data-ttu-id="823d5-241">有关在 EF 和如何访问和处理数据使用关系的关系的常规信息，请参阅[关系和导航属性](~/ef6/fundamentals/relationships.md)。 \*</span><span class="sxs-lookup"><span data-stu-id="823d5-241">For general information about relationships in EF and how to access and manipulate data using relationships, see [Relationships & Navigation Properties](~/ef6/fundamentals/relationships.md).\*</span></span>

<span data-ttu-id="823d5-242">第一个代码约定将负责在模型中最常见的关系，但有某些情况下，应帮助在位置。</span><span class="sxs-lookup"><span data-stu-id="823d5-242">Code first convention will take care of the most common relationships in your model, but there are some cases where it needs help.</span></span>

<span data-ttu-id="823d5-243">更改造成的问题及其与文章的博客类中的键属性的名称。</span><span class="sxs-lookup"><span data-stu-id="823d5-243">Changing the name of the key property in the Blog class created a problem with its relationship to Post.</span></span> 

<span data-ttu-id="823d5-244">在生成数据库时，代码将首先看到在开机自检类的 BlogId 属性，并识别它，通过它与类名以及"Id"，匹配作为博客类的外键的约定。</span><span class="sxs-lookup"><span data-stu-id="823d5-244">When generating the database, code first sees the BlogId property in the Post class and recognizes it, by the convention that it matches a class name plus “Id”, as a foreign key to the Blog class.</span></span> <span data-ttu-id="823d5-245">但在博客类中没有任何 BlogId 属性。</span><span class="sxs-lookup"><span data-stu-id="823d5-245">But there is no BlogId property in the blog class.</span></span> <span data-ttu-id="823d5-246">此问题的解决方案是在 Post 中创建导航属性并使用外 DataAnnotation 帮助代码首先了解如何生成两个类之间的关系，使用 Post.BlogId 属性，以及如何指定中的约束数据库。</span><span class="sxs-lookup"><span data-stu-id="823d5-246">The solution for this is to create a navigation property in the Post and use the Foreign DataAnnotation to help code first understand how to build the relationship between the two classes —using the Post.BlogId property — as well as how to specify constraints in the database.</span></span>

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

<span data-ttu-id="823d5-247">在数据库中的约束演示 InternalBlogs.PrimaryTrackingKey 和 Posts.BlogId 之间的关系。</span><span class="sxs-lookup"><span data-stu-id="823d5-247">The constraint in the database shows a relationship between InternalBlogs.PrimaryTrackingKey and Posts.BlogId.</span></span> 

![InternalBlogs.PrimaryTrackingKey 与 Posts.BlogId 之间的关系](~/ef6/media/jj591583-figure09.png)

<span data-ttu-id="823d5-249">有多个类之间的关系时，使用 InverseProperty。</span><span class="sxs-lookup"><span data-stu-id="823d5-249">The InverseProperty is used when you have multiple relationships between classes.</span></span>

<span data-ttu-id="823d5-250">在发布类中，你可能想要跟踪谁编写了一篇博客文章的以及谁对其进行编辑。</span><span class="sxs-lookup"><span data-stu-id="823d5-250">In the Post class, you may want to keep track of who wrote a blog post as well as who edited it.</span></span> <span data-ttu-id="823d5-251">以下是 Post 类的两个新的导航属性。</span><span class="sxs-lookup"><span data-stu-id="823d5-251">Here are two new navigation properties for the Post class.</span></span>

``` csharp
    public Person CreatedBy { get; set; }
    public Person UpdatedBy { get; set; }
```

<span data-ttu-id="823d5-252">此外需要引用这些属性的 Person 类中添加。</span><span class="sxs-lookup"><span data-stu-id="823d5-252">You’ll also need to add in the Person class referenced by these properties.</span></span> <span data-ttu-id="823d5-253">Person 类具有导航属性返回到 Post，一个用于所有的人，一个用于更新由该作者的文章的所有文章。</span><span class="sxs-lookup"><span data-stu-id="823d5-253">The Person class has navigation properties back to the Post, one for all of the posts written by the person and one for all of the posts updated by that person.</span></span>

``` csharp
    public class Person
    {
            public int Id { get; set; }
            public string Name { get; set; }
            public List<Post> PostsWritten { get; set; }
            public List<Post> PostsUpdated { get; set; }
    }
```

<span data-ttu-id="823d5-254">代码首先不能在其自己的两个类中的属性匹配。</span><span class="sxs-lookup"><span data-stu-id="823d5-254">Code first is not able to match up the properties in the two classes on its own.</span></span> <span data-ttu-id="823d5-255">文章的数据库表应拥有的 CreatedBy 人员，另一个用于 UpdatedBy 人员的一个外键，但代码首先将创建四个外键属性：Person\_Id、 人\_Id1、 CreatedBy\_Id 和 UpdatedBy\_id。</span><span class="sxs-lookup"><span data-stu-id="823d5-255">The database table for Posts should have one foreign key for the CreatedBy person and one for the UpdatedBy person but code first will create four foreign key properties: Person\_Id, Person\_Id1, CreatedBy\_Id and UpdatedBy\_Id.</span></span>

![Posts 表包含的额外外键](~/ef6/media/jj591583-figure10.png)

<span data-ttu-id="823d5-257">若要解决这些问题，可以使用 InverseProperty 批注指定属性的对齐方式。</span><span class="sxs-lookup"><span data-stu-id="823d5-257">To fix these problems, you can use the InverseProperty annotation to specify the alignment of the properties.</span></span>

``` csharp
    [InverseProperty("CreatedBy")]
    public List<Post> PostsWritten { get; set; }

    [InverseProperty("UpdatedBy")]
    public List<Post> PostsUpdated { get; set; }
```

<span data-ttu-id="823d5-258">亲自 PostsWritten 属性就知道，这是指 Post 类型，因为它将生成与 Post.CreatedBy 之间的关系。</span><span class="sxs-lookup"><span data-stu-id="823d5-258">Because the PostsWritten property in Person knows that this refers to the Post type, it will build the relationship to Post.CreatedBy.</span></span> <span data-ttu-id="823d5-259">同样，PostsUpdated 将连接到 Post.UpdatedBy。</span><span class="sxs-lookup"><span data-stu-id="823d5-259">Similarly, PostsUpdated will be connected to Post.UpdatedBy.</span></span> <span data-ttu-id="823d5-260">且代码首先会创建额外外键。</span><span class="sxs-lookup"><span data-stu-id="823d5-260">And code first will not create the extra foreign keys.</span></span>

![Posts 表，而无需额外外键](~/ef6/media/jj591583-figure11.png)

 

## <a name="summary"></a><span data-ttu-id="823d5-262">总结</span><span class="sxs-lookup"><span data-stu-id="823d5-262">Summary</span></span>

<span data-ttu-id="823d5-263">DataAnnotations 不仅允许您描述客户端和服务器端验证在您的代码的第一个类，但它们还使你可以增强和甚至更正代码首先将使有关基于其约定类的假设。</span><span class="sxs-lookup"><span data-stu-id="823d5-263">DataAnnotations not only let you describe client and server side validation in your code first classes, but they also allow you to enhance and even correct the assumptions that code first will make about your classes based on its conventions.</span></span> <span data-ttu-id="823d5-264">使用 DataAnnotations，可不只让数据库架构生成，但还可以将您的代码的第一类映射到预先存在的数据库。</span><span class="sxs-lookup"><span data-stu-id="823d5-264">With DataAnnotations you can not only drive database schema generation, but you can also map your code first classes to a pre-existing database.</span></span>

<span data-ttu-id="823d5-265">尽管它们非常灵活，请记住，DataAnnotations 提供仅大多数通常所需的配置更改可以使您的代码的第一个类。</span><span class="sxs-lookup"><span data-stu-id="823d5-265">While they are very flexible, keep in mind that DataAnnotations provide only the most commonly needed configuration changes you can make on your code first classes.</span></span> <span data-ttu-id="823d5-266">若要配置您的类的一些边缘情况，您应查找的备用配置机制，Code First Fluent API。</span><span class="sxs-lookup"><span data-stu-id="823d5-266">To configure your classes for some of the edge cases, you should look to the alternate configuration mechanism, Code First’s Fluent API .</span></span>
