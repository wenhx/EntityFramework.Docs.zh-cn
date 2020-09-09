---
title: 自定义 Code First 约定-EF6
description: 实体框架6中的自定义 Code First 约定
author: divega
ms.date: 10/23/2016
ms.assetid: dd2bdbd9-ae9e-470a-aeb8-d0ba160499b7
uid: ef6/modeling/code-first/conventions/custom
ms.openlocfilehash: 69e4b0111394e83195f5c0a81624c7b9e45bda52
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617263"
---
# <a name="custom-code-first-conventions"></a><span data-ttu-id="1b52b-103">自定义 Code First 约定</span><span class="sxs-lookup"><span data-stu-id="1b52b-103">Custom Code First Conventions</span></span>
> [!NOTE]
> <span data-ttu-id="1b52b-104">**仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。</span><span class="sxs-lookup"><span data-stu-id="1b52b-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="1b52b-105">如果使用的是早期版本，则部分或全部信息不适用。</span><span class="sxs-lookup"><span data-stu-id="1b52b-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="1b52b-106">使用时 Code First 模型将使用一组约定从类进行计算。</span><span class="sxs-lookup"><span data-stu-id="1b52b-106">When using Code First your model is calculated from your classes using a set of conventions.</span></span> <span data-ttu-id="1b52b-107">默认 [Code First 约定](xref:ef6/modeling/code-first/conventions/built-in) 确定哪些属性将成为实体的主键、实体映射到的表的名称，以及在默认情况下，小数列的精度和小数位数。</span><span class="sxs-lookup"><span data-stu-id="1b52b-107">The default [Code First Conventions](xref:ef6/modeling/code-first/conventions/built-in) determine things like which property becomes the primary key of an entity, the name of the table an entity maps to, and what precision and scale a decimal column has by default.</span></span>

<span data-ttu-id="1b52b-108">有时，这些默认约定并不适用于您的模型，并且您必须通过使用数据注释或流畅的 API 来配置多个单独的实体来解决它们。</span><span class="sxs-lookup"><span data-stu-id="1b52b-108">Sometimes these default conventions are not ideal for your model, and you have to work around them by configuring many individual entities using Data Annotations or the Fluent API.</span></span> <span data-ttu-id="1b52b-109">自定义 Code First 约定允许您定义自己的约定，为模型提供配置默认值。</span><span class="sxs-lookup"><span data-stu-id="1b52b-109">Custom Code First Conventions let you define your own conventions that provide configuration defaults for your model.</span></span> <span data-ttu-id="1b52b-110">在本演练中，我们将探讨不同类型的自定义约定以及如何创建它们。</span><span class="sxs-lookup"><span data-stu-id="1b52b-110">In this walkthrough, we will explore the different types of custom conventions and how to create each of them.</span></span>


## <a name="model-based-conventions"></a><span data-ttu-id="1b52b-111">基于模型的约定</span><span class="sxs-lookup"><span data-stu-id="1b52b-111">Model-Based Conventions</span></span>

<span data-ttu-id="1b52b-112">本页介绍用于自定义约定的 DbModelBuilder API。</span><span class="sxs-lookup"><span data-stu-id="1b52b-112">This page covers the DbModelBuilder API for custom conventions.</span></span> <span data-ttu-id="1b52b-113">此 API 应该足以用于创作大多数自定义约定。</span><span class="sxs-lookup"><span data-stu-id="1b52b-113">This API should be sufficient for authoring most custom conventions.</span></span> <span data-ttu-id="1b52b-114">但是，还可以创作基于模型的约定-在创建最终模型后对其进行操作的约定-用于处理高级方案。</span><span class="sxs-lookup"><span data-stu-id="1b52b-114">However, there is also the ability to author model-based conventions - conventions that manipulate the final model once it is created - to handle advanced scenarios.</span></span> <span data-ttu-id="1b52b-115">有关详细信息，请参阅 [基于模型的约定](xref:ef6/modeling/code-first/conventions/model)。</span><span class="sxs-lookup"><span data-stu-id="1b52b-115">For more information, see [Model-Based Conventions](xref:ef6/modeling/code-first/conventions/model).</span></span>

 

## <a name="our-model"></a><span data-ttu-id="1b52b-116">我们的模型</span><span class="sxs-lookup"><span data-stu-id="1b52b-116">Our Model</span></span>

<span data-ttu-id="1b52b-117">首先，我们定义一个可用于约定的简单模型。</span><span class="sxs-lookup"><span data-stu-id="1b52b-117">Let's start by defining a simple model that we can use with our conventions.</span></span> <span data-ttu-id="1b52b-118">将以下类添加到项目。</span><span class="sxs-lookup"><span data-stu-id="1b52b-118">Add the following classes to your project.</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Linq;

    public class ProductContext : DbContext
    {
        static ProductContext()
        {
            Database.SetInitializer(new DropCreateDatabaseIfModelChanges<ProductContext>());
        }

        public DbSet<Product> Products { get; set; }
    }

    public class Product
    {
        public int Key { get; set; }
        public string Name { get; set; }
        public decimal? Price { get; set; }
        public DateTime? ReleaseDate { get; set; }
        public ProductCategory Category { get; set; }
    }

    public class ProductCategory
    {
        public int Key { get; set; }
        public string Name { get; set; }
        public List<Product> Products { get; set; }
    }
```

 

## <a name="introducing-custom-conventions"></a><span data-ttu-id="1b52b-119">自定义约定简介</span><span class="sxs-lookup"><span data-stu-id="1b52b-119">Introducing Custom Conventions</span></span>

<span data-ttu-id="1b52b-120">我们来编写一种约定，将名为 Key 的任何属性配置为其实体类型的主键。</span><span class="sxs-lookup"><span data-stu-id="1b52b-120">Let’s write a convention that configures any property named Key to be the primary key for its entity type.</span></span>

<span data-ttu-id="1b52b-121">在模型生成器上启用约定，可以通过在上下文中重写 OnModelCreating 来访问这些约定。</span><span class="sxs-lookup"><span data-stu-id="1b52b-121">Conventions are enabled on the model builder, which can be accessed by overriding OnModelCreating in the context.</span></span> <span data-ttu-id="1b52b-122">更新 ProductContext 类，如下所示：</span><span class="sxs-lookup"><span data-stu-id="1b52b-122">Update the ProductContext class as follows:</span></span>

``` csharp
    public class ProductContext : DbContext
    {
        static ProductContext()
        {
            Database.SetInitializer(new DropCreateDatabaseIfModelChanges<ProductContext>());
        }

        public DbSet<Product> Products { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Properties()
                        .Where(p => p.Name == "Key")
                        .Configure(p => p.IsKey());
        }
    }
```

<span data-ttu-id="1b52b-123">现在，模型中名为 Key 的任何属性都将配置为其一部分的任何实体的主键。</span><span class="sxs-lookup"><span data-stu-id="1b52b-123">Now, any property in our model named Key will be configured as the primary key of whatever entity its part of.</span></span>

<span data-ttu-id="1b52b-124">还可以通过筛选要配置的属性类型，使约定更加具体：</span><span class="sxs-lookup"><span data-stu-id="1b52b-124">We could also make our conventions more specific by filtering on the type of property that we are going to configure:</span></span>

``` csharp
    modelBuilder.Properties<int>()
                .Where(p => p.Name == "Key")
                .Configure(p => p.IsKey());
```

<span data-ttu-id="1b52b-125">这会将名为 Key 的所有属性配置为其实体的主键，但前提是它们是整数。</span><span class="sxs-lookup"><span data-stu-id="1b52b-125">This will configure all properties called Key to be the primary key of their entity, but only if they are an integer.</span></span>

<span data-ttu-id="1b52b-126">IsKey 方法的一个有趣功能是它是附加的。</span><span class="sxs-lookup"><span data-stu-id="1b52b-126">An interesting feature of the IsKey method is that it is additive.</span></span> <span data-ttu-id="1b52b-127">这意味着，如果在多个属性上调用 IsKey，它们都将成为复合键的一部分。</span><span class="sxs-lookup"><span data-stu-id="1b52b-127">Which means that if you call IsKey on multiple properties and they will all become part of a composite key.</span></span> <span data-ttu-id="1b52b-128">需要注意的一点是，如果为某个键指定了多个属性，还必须为这些属性指定顺序。</span><span class="sxs-lookup"><span data-stu-id="1b52b-128">The one caveat for this is that when you specify multiple properties for a key you must also specify an order for those properties.</span></span> <span data-ttu-id="1b52b-129">为此，可以调用 HasColumnOrder 方法，如下所示：</span><span class="sxs-lookup"><span data-stu-id="1b52b-129">You can do this by calling the HasColumnOrder method like below:</span></span>

``` csharp
    modelBuilder.Properties<int>()
                .Where(x => x.Name == "Key")
                .Configure(x => x.IsKey().HasColumnOrder(1));

    modelBuilder.Properties()
                .Where(x => x.Name == "Name")
                .Configure(x => x.IsKey().HasColumnOrder(2));
```

<span data-ttu-id="1b52b-130">此代码将配置模型中的类型，使其包含 int 键列和字符串名称列组成的组合键。</span><span class="sxs-lookup"><span data-stu-id="1b52b-130">This code will configure the types in our model to have a composite key consisting of the int Key column and the string Name column.</span></span> <span data-ttu-id="1b52b-131">如果在设计器中查看模型，它将如下所示：</span><span class="sxs-lookup"><span data-stu-id="1b52b-131">If we view the model in the designer it would look like this:</span></span>

![组合键](~/ef6/media/compositekey.png)

<span data-ttu-id="1b52b-133">属性约定的另一个示例是将我的模型中的所有日期时间属性配置为映射到 SQL Server 中的 datetime2 类型，而不是 DateTime。</span><span class="sxs-lookup"><span data-stu-id="1b52b-133">Another example of property conventions is to configure all DateTime properties in my model to map to the datetime2 type in SQL Server instead of datetime.</span></span> <span data-ttu-id="1b52b-134">可以通过以下方式实现此目的：</span><span class="sxs-lookup"><span data-stu-id="1b52b-134">You can achieve this with the following:</span></span>

``` csharp
    modelBuilder.Properties<DateTime>()
                .Configure(c => c.HasColumnType("datetime2"));
```

 

## <a name="convention-classes"></a><span data-ttu-id="1b52b-135">约定类</span><span class="sxs-lookup"><span data-stu-id="1b52b-135">Convention Classes</span></span>

<span data-ttu-id="1b52b-136">定义约定的另一种方法是使用约定类封装约定。</span><span class="sxs-lookup"><span data-stu-id="1b52b-136">Another way of defining conventions is to use a Convention Class to encapsulate your convention.</span></span> <span data-ttu-id="1b52b-137">使用约定类时，将创建一个从 ModelConfiguration 命名空间中的约定类继承的类型。</span><span class="sxs-lookup"><span data-stu-id="1b52b-137">When using a Convention Class then you create a type that inherits from the Convention class in the System.Data.Entity.ModelConfiguration.Conventions namespace.</span></span>

<span data-ttu-id="1b52b-138">可以通过执行以下操作，创建一个具有前文约定的约定类：</span><span class="sxs-lookup"><span data-stu-id="1b52b-138">We can create a Convention Class with the datetime2 convention that we showed earlier by doing the following:</span></span>

``` csharp
    public class DateTime2Convention : Convention
    {
        public DateTime2Convention()
        {
            this.Properties<DateTime>()
                .Configure(c => c.HasColumnType("datetime2"));        
        }
    }
```

<span data-ttu-id="1b52b-139">若要告诉 EF 使用此约定，请将其添加到 OnModelCreating 中的约定集合，如果已执行以下操作，演练将如下所示：</span><span class="sxs-lookup"><span data-stu-id="1b52b-139">To tell EF to use this convention you add it to the Conventions collection in OnModelCreating, which if you’ve been following along with the walkthrough will look like this:</span></span>

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Properties<int>()
                    .Where(p => p.Name.EndsWith("Key"))
                    .Configure(p => p.IsKey());

        modelBuilder.Conventions.Add(new DateTime2Convention());
    }
```

<span data-ttu-id="1b52b-140">如您所见，我们将约定的实例添加到约定集合。</span><span class="sxs-lookup"><span data-stu-id="1b52b-140">As you can see we add an instance of our convention to the conventions collection.</span></span> <span data-ttu-id="1b52b-141">从约定继承提供了一种在团队或项目之间进行分组和共享约定的便利方法。</span><span class="sxs-lookup"><span data-stu-id="1b52b-141">Inheriting from Convention provides a convenient way of grouping and sharing conventions across teams or projects.</span></span> <span data-ttu-id="1b52b-142">例如，你可以有一个类库，其中包含所有组织项目使用的一组通用约定。</span><span class="sxs-lookup"><span data-stu-id="1b52b-142">You could, for example, have a class library with a common set of conventions that all of your organizations projects use.</span></span>

 

## <a name="custom-attributes"></a><span data-ttu-id="1b52b-143">自定义特性</span><span class="sxs-lookup"><span data-stu-id="1b52b-143">Custom Attributes</span></span>

<span data-ttu-id="1b52b-144">约定的另一种很好的用途是启用在配置模型时使用的新属性。</span><span class="sxs-lookup"><span data-stu-id="1b52b-144">Another great use of conventions is to enable new attributes to be used when configuring a model.</span></span> <span data-ttu-id="1b52b-145">为了说明这一点，让我们创建一个属性，用于将字符串属性标记为非 Unicode。</span><span class="sxs-lookup"><span data-stu-id="1b52b-145">To illustrate this, let’s create an attribute that we can use to mark String properties as non-Unicode.</span></span>

``` csharp
    [AttributeUsage(AttributeTargets.Property, AllowMultiple = false)]
    public class NonUnicode : Attribute
    {
    }
```

<span data-ttu-id="1b52b-146">现在，让我们创建一个约定，以将此属性应用于模型：</span><span class="sxs-lookup"><span data-stu-id="1b52b-146">Now, let’s create a convention to apply this attribute to our model:</span></span>

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<NonUnicode>().Any())
                .Configure(c => c.IsUnicode(false));
```

<span data-ttu-id="1b52b-147">通过此约定，我们可以将 NonUnicode 属性添加到字符串的任何属性，这意味着数据库中的列将存储为 varchar 而不是 nvarchar。</span><span class="sxs-lookup"><span data-stu-id="1b52b-147">With this convention we can add the NonUnicode attribute to any of our string properties, which means the column in the database will be stored as varchar instead of nvarchar.</span></span>

<span data-ttu-id="1b52b-148">关于此约定，需要注意的一点是，如果将 NonUnicode 属性放在字符串属性以外的任何内容中，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="1b52b-148">One thing to note about this convention is that if you put the NonUnicode attribute on anything other than a string property then it will throw an exception.</span></span> <span data-ttu-id="1b52b-149">这是因为不能在字符串以外的任何类型上配置 IsUnicode。</span><span class="sxs-lookup"><span data-stu-id="1b52b-149">It does this because you cannot configure IsUnicode on any type other than a string.</span></span> <span data-ttu-id="1b52b-150">如果发生这种情况，则可以使约定更为具体，使其筛选掉不是字符串的任何内容。</span><span class="sxs-lookup"><span data-stu-id="1b52b-150">If this happens, then you can make your convention more specific, so that it filters out anything that isn’t a string.</span></span>

<span data-ttu-id="1b52b-151">尽管上述约定适用于定义自定义属性，但还有另一个 API 可以更容易地使用，尤其是在你想要使用属性类中的属性时。</span><span class="sxs-lookup"><span data-stu-id="1b52b-151">While the above convention works for defining custom attributes there is another API that can be much easier to use, especially when you want to use properties from the attribute class.</span></span>

<span data-ttu-id="1b52b-152">在此示例中，我们将更新属性，并将其更改为 IsUnicode 属性，如下所示：</span><span class="sxs-lookup"><span data-stu-id="1b52b-152">For this example we are going to update our attribute and change it to an IsUnicode attribute, so it looks like this:</span></span>

``` csharp
    [AttributeUsage(AttributeTargets.Property, AllowMultiple = false)]
    internal class IsUnicode : Attribute
    {
        public bool Unicode { get; set; }

        public IsUnicode(bool isUnicode)
        {
            Unicode = isUnicode;
        }
    }
```

<span data-ttu-id="1b52b-153">完成此设置后，可以在属性上设置一个布尔值，以指示该属性是否应为 Unicode。</span><span class="sxs-lookup"><span data-stu-id="1b52b-153">Once we have this, we can set a bool on our attribute to tell the convention whether or not a property should be Unicode.</span></span> <span data-ttu-id="1b52b-154">为此，我们可以通过访问配置类的 ClrProperty 来执行此操作，如下所示：</span><span class="sxs-lookup"><span data-stu-id="1b52b-154">We could do this in the convention we have already by accessing the ClrProperty of the configuration class like this:</span></span>

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<IsUnicode>().Any())
                .Configure(c => c.IsUnicode(c.ClrPropertyInfo.GetCustomAttribute<IsUnicode>().Unicode));
```

<span data-ttu-id="1b52b-155">这非常简单，但通过使用约定 API 的 Having 方法，可以更简洁地实现此方法。</span><span class="sxs-lookup"><span data-stu-id="1b52b-155">This is easy enough, but there is a more succinct way of achieving this by using the Having method of the conventions API.</span></span> <span data-ttu-id="1b52b-156">Having 方法具有类型为 Func &lt; PropertyInfo、T 的参数，该参数 &gt; 接受与 Where 方法相同的 PropertyInfo，但预期返回对象。</span><span class="sxs-lookup"><span data-stu-id="1b52b-156">The Having method has a parameter of type Func&lt;PropertyInfo, T&gt; which accepts the PropertyInfo the same as the Where method, but is expected to return an object.</span></span> <span data-ttu-id="1b52b-157">如果返回的对象为 null，则不会配置属性，这意味着，你可以像在中那样筛选 out 属性，但不同之处在于它还将捕获返回的对象并将其传递给 Configure 方法。</span><span class="sxs-lookup"><span data-stu-id="1b52b-157">If the returned object is null then the property will not be configured, which means you can filter out properties with it just like Where, but it is different in that it will also capture the returned object and pass it to the Configure method.</span></span> <span data-ttu-id="1b52b-158">这类似于以下内容：</span><span class="sxs-lookup"><span data-stu-id="1b52b-158">This works like the following:</span></span>

``` csharp
    modelBuilder.Properties()
                .Having(x =>x.GetCustomAttributes(false).OfType<IsUnicode>().FirstOrDefault())
                .Configure((config, att) => config.IsUnicode(att.Unicode));
```

<span data-ttu-id="1b52b-159">自定义特性并不是使用 Having 方法的唯一理由，在配置类型或属性时，您需要对其进行筛选的任何位置都很有用。</span><span class="sxs-lookup"><span data-stu-id="1b52b-159">Custom attributes are not the only reason to use the Having method, it is useful anywhere that you need to reason about something that you are filtering on when configuring your types or properties.</span></span>

 

## <a name="configuring-types"></a><span data-ttu-id="1b52b-160">配置类型</span><span class="sxs-lookup"><span data-stu-id="1b52b-160">Configuring Types</span></span>

<span data-ttu-id="1b52b-161">到目前为止，我们的所有约定都是关于属性的，但也有另一个用于配置模型中的类型的约定 API 区域。</span><span class="sxs-lookup"><span data-stu-id="1b52b-161">So far all of our conventions have been for properties, but there is another area of the conventions API for configuring the types in your model.</span></span> <span data-ttu-id="1b52b-162">经验类似于我们目前所见到的约定，但配置内的选项将位于实体而不是属性级别。</span><span class="sxs-lookup"><span data-stu-id="1b52b-162">The experience is similar to the conventions we have seen so far, but the options inside configure will be at the entity instead of property level.</span></span>

<span data-ttu-id="1b52b-163">类型级别约定的一项功能对于更改表命名约定非常有用，它可以映射到与 EF 默认值不同的现有架构，也可以使用不同的命名约定来创建新的数据库。</span><span class="sxs-lookup"><span data-stu-id="1b52b-163">One of the things that Type level conventions can be really useful for is changing the table naming convention, either to map to an existing schema that differs from the EF default or to create a new database with a different naming convention.</span></span> <span data-ttu-id="1b52b-164">为此，我们首先需要一个方法，该方法可以接受模型中类型的 TypeInfo，并返回该类型的表名称应为：</span><span class="sxs-lookup"><span data-stu-id="1b52b-164">To do this we first need a method that can accept the TypeInfo for a type in our model and return what the table name for that type should be:</span></span>

``` csharp
    private string GetTableName(Type type)
    {
        var result = Regex.Replace(type.Name, ".[A-Z]", m => m.Value[0] + "_" + m.Value[1]);

        return result.ToLower();
    }
```

<span data-ttu-id="1b52b-165">此方法采用类型并返回一个字符串，该字符串使用带下划线的小写形式而不是 CamelCase。</span><span class="sxs-lookup"><span data-stu-id="1b52b-165">This method takes a type and returns a string that uses lower case with underscores instead of CamelCase.</span></span> <span data-ttu-id="1b52b-166">在我们的模型中，这意味着 ProductCategory 类将映射到称为 product \_ category 的表，而不是 ProductCategories。</span><span class="sxs-lookup"><span data-stu-id="1b52b-166">In our model this means that the ProductCategory class will be mapped to a table called product\_category instead of ProductCategories.</span></span>

<span data-ttu-id="1b52b-167">获得该方法后，我们可以在此类约定中调用它：</span><span class="sxs-lookup"><span data-stu-id="1b52b-167">Once we have that method we can call it in a convention like this:</span></span>

``` csharp
    modelBuilder.Types()
                .Configure(c => c.ToTable(GetTableName(c.ClrType)));
```

<span data-ttu-id="1b52b-168">此约定将模型中的每个类型配置为映射到 GetTableName 方法返回的表名。</span><span class="sxs-lookup"><span data-stu-id="1b52b-168">This convention configures every type in our model to map to the table name that is returned from our GetTableName method.</span></span> <span data-ttu-id="1b52b-169">此约定等效于使用熟知 API 为模型中的每个实体调用 ToTable 方法。</span><span class="sxs-lookup"><span data-stu-id="1b52b-169">This convention is the equivalent to calling the ToTable method for each entity in the model using the Fluent API.</span></span>

<span data-ttu-id="1b52b-170">需要注意的一点是，当你调用 ToTable EF 时，将采用你提供的字符串作为确切的表名称，而不是在确定表名称时通常会执行的任何复数形式。</span><span class="sxs-lookup"><span data-stu-id="1b52b-170">One thing to note about this is that when you call ToTable EF will take the string that you provide as the exact table name, without any of the pluralization that it would normally do when determining table names.</span></span> <span data-ttu-id="1b52b-171">这就是我们的约定中的表名是产品 \_ 类别而不是产品类别的原因 \_ 。</span><span class="sxs-lookup"><span data-stu-id="1b52b-171">This is why the table name from our convention is product\_category instead of product\_categories.</span></span> <span data-ttu-id="1b52b-172">我们可以通过调用复数形式服务自己的约定来解决这一问题。</span><span class="sxs-lookup"><span data-stu-id="1b52b-172">We can resolve that in our convention by making a call to the pluralization service ourselves.</span></span>

<span data-ttu-id="1b52b-173">在下面的代码中，我们将使用 EF6 中添加的 [依赖项解析](xref:ef6/fundamentals/configuring/dependency-resolution) 功能检索 EF 使用的复数形式服务并复数形式我们的表名。</span><span class="sxs-lookup"><span data-stu-id="1b52b-173">In the following code we will use the [Dependency Resolution](xref:ef6/fundamentals/configuring/dependency-resolution) feature added in EF6 to retrieve the pluralization service that EF would have used and pluralize our table name.</span></span>

``` csharp
    private string GetTableName(Type type)
    {
        var pluralizationService = DbConfiguration.DependencyResolver.GetService<IPluralizationService>();

        var result = pluralizationService.Pluralize(type.Name);

        result = Regex.Replace(result, ".[A-Z]", m => m.Value[0] + "_" + m.Value[1]);

        return result.ToLower();
    }
```

> [!NOTE]
> <span data-ttu-id="1b52b-174">GetService 的泛型版本是 DependencyResolution 命名空间中的扩展方法，需要将 using 语句添加到你的上下文中才能使用该语句。</span><span class="sxs-lookup"><span data-stu-id="1b52b-174">The generic version of GetService is an extension method in the System.Data.Entity.Infrastructure.DependencyResolution namespace, you will need to add a using statement to your context in order to use it.</span></span>

### <a name="totable-and-inheritance"></a><span data-ttu-id="1b52b-175">ToTable 和继承</span><span class="sxs-lookup"><span data-stu-id="1b52b-175">ToTable and Inheritance</span></span>

<span data-ttu-id="1b52b-176">ToTable 的另一个重要方面是，如果您将某一类型显式映射到给定的表，则可以更改 EF 将使用的映射策略。</span><span class="sxs-lookup"><span data-stu-id="1b52b-176">Another important aspect of ToTable is that if you explicitly map a type to a given table, then you can alter the mapping strategy that EF will use.</span></span> <span data-ttu-id="1b52b-177">如果为继承层次结构中的每个类型调用 ToTable，并将类型名称作为表的名称传递，则会将默认的每个层次结构一个表 (TPH) 映射策略更改为每种类型一个表 (TPT) 。</span><span class="sxs-lookup"><span data-stu-id="1b52b-177">If you call ToTable for every type in an inheritance hierarchy, passing the type name as the name of the table like we did above, then you will change the default Table-Per-Hierarchy (TPH) mapping strategy to Table-Per-Type (TPT).</span></span> <span data-ttu-id="1b52b-178">描述这一点的最佳方式是 whith 一个具体的示例：</span><span class="sxs-lookup"><span data-stu-id="1b52b-178">The best way to describe this is whith a concrete example:</span></span>

``` csharp
    public class Employee
    {
        public int Id { get; set; }
        public string Name { get; set; }
    }

    public class Manager : Employee
    {
        public string SectionManaged { get; set; }
    }
```

<span data-ttu-id="1b52b-179">默认情况下，员工和经理都映射到数据库中 (员工) 的同一个表。</span><span class="sxs-lookup"><span data-stu-id="1b52b-179">By default both employee and manager are mapped to the same table (Employees) in the database.</span></span> <span data-ttu-id="1b52b-180">该表将包含具有鉴别器列的员工和经理，该列将告诉您每一行中存储的实例类型。</span><span class="sxs-lookup"><span data-stu-id="1b52b-180">The table will contain both employees and managers with a discriminator column that will tell you what type of instance is stored in each row.</span></span> <span data-ttu-id="1b52b-181">这是 TPH 映射，因为层次结构中有一个表。</span><span class="sxs-lookup"><span data-stu-id="1b52b-181">This is TPH mapping as there is a single table for the hierarchy.</span></span> <span data-ttu-id="1b52b-182">但是，如果在这两个类上调用 ToTable，则每个类型都将映射到其自己的表，也称为 TPT，因为每个类型都有其自己的表。</span><span class="sxs-lookup"><span data-stu-id="1b52b-182">However, if you call ToTable on both classe then each type will instead be mapped to its own table, also known as TPT since each type has its own table.</span></span>

``` csharp
    modelBuilder.Types()
                .Configure(c=>c.ToTable(c.ClrType.Name));
```

<span data-ttu-id="1b52b-183">上面的代码将映射到如下所示的表结构：</span><span class="sxs-lookup"><span data-stu-id="1b52b-183">The code above will map to a table structure that looks like the following:</span></span>

![tpt 示例](~/ef6/media/tptexample.jpg)

<span data-ttu-id="1b52b-185">可以通过以下几种方式来避免这种情况并维护默认的 TPH 映射：</span><span class="sxs-lookup"><span data-stu-id="1b52b-185">You can avoid this, and maintain the default TPH mapping, in a couple ways:</span></span>

1.  <span data-ttu-id="1b52b-186">为层次结构中的每个类型调用具有相同表名称的 ToTable。</span><span class="sxs-lookup"><span data-stu-id="1b52b-186">Call ToTable with the same table name for each type in the hierarchy.</span></span>
2.  <span data-ttu-id="1b52b-187">仅在层次结构的基类（在本示例中为 employee）上调用 ToTable。</span><span class="sxs-lookup"><span data-stu-id="1b52b-187">Call ToTable only on the base class of the hierarchy, in our example that would be employee.</span></span>

 

## <a name="execution-order"></a><span data-ttu-id="1b52b-188">执行顺序</span><span class="sxs-lookup"><span data-stu-id="1b52b-188">Execution Order</span></span>

<span data-ttu-id="1b52b-189">约定的运行方式与精通 API 相同。</span><span class="sxs-lookup"><span data-stu-id="1b52b-189">Conventions operate in a last wins manner, the same as the Fluent API.</span></span> <span data-ttu-id="1b52b-190">这意味着，如果您编写两个约定来配置同一属性的相同选项，最后一个规则执行 wins。</span><span class="sxs-lookup"><span data-stu-id="1b52b-190">What this means is that if you write two conventions that configure the same option of the same property, then the last one to execute wins.</span></span> <span data-ttu-id="1b52b-191">例如，在下面的代码中，所有字符串的最大长度都设置为500，但然后，在模型中将名为 Name 的所有属性都配置为具有最大长度250。</span><span class="sxs-lookup"><span data-stu-id="1b52b-191">As an example, in the code below the max length of all strings is set to 500 but we then configure all properties called Name in the model to have a max length of 250.</span></span>

``` csharp
    modelBuilder.Properties<string>()
                .Configure(c => c.HasMaxLength(500));

    modelBuilder.Properties<string>()
                .Where(x => x.Name == "Name")
                .Configure(c => c.HasMaxLength(250));
```

<span data-ttu-id="1b52b-192">由于将最大长度设置为250的约定是在将所有字符串设置为500后，因此模型中名为 "名称" 的所有属性的 MaxLength 都为250，而任何其他字符串（如 "说明"）都是500。</span><span class="sxs-lookup"><span data-stu-id="1b52b-192">Because the convention to set max length to 250 is after the one that sets all strings to 500, all the properties called Name in our model will have a MaxLength of 250 while any other strings, such as descriptions, would be 500.</span></span> <span data-ttu-id="1b52b-193">以这种方式使用约定意味着你可以为模型中的类型或属性提供一般约定，然后将其覆盖为不同的子集。</span><span class="sxs-lookup"><span data-stu-id="1b52b-193">Using conventions in this way means that you can provide a general convention for types or properties in your model and then overide them for subsets that are different.</span></span>

<span data-ttu-id="1b52b-194">在特定情况下，还可以使用熟知的 API 和数据批注来替代约定。</span><span class="sxs-lookup"><span data-stu-id="1b52b-194">The Fluent API and Data Annotations can also be used to override a convention in specific cases.</span></span> <span data-ttu-id="1b52b-195">在上面的示例中，如果我们使用了 "熟知 API" 设置属性的最大长度，则可以将其放在约定之前或之后，因为更具体的熟知 API 将通过更常规的配置约定。</span><span class="sxs-lookup"><span data-stu-id="1b52b-195">In our example above if we had used the Fluent API to set the max length of a property then we could have put it before or after the convention, because the more specific Fluent API will win over the more general Configuration Convention.</span></span>

 

## <a name="built-in-conventions"></a><span data-ttu-id="1b52b-196">内置约定</span><span class="sxs-lookup"><span data-stu-id="1b52b-196">Built-in Conventions</span></span>

<span data-ttu-id="1b52b-197">由于自定义约定可能受默认 Code First 约定的影响，因此添加约定以在其他约定之前或之后运行可能会很有用。</span><span class="sxs-lookup"><span data-stu-id="1b52b-197">Because custom conventions could be affected by the default Code First conventions, it can be useful to add conventions to run before or after another convention.</span></span> <span data-ttu-id="1b52b-198">为此，可以在派生的 DbContext 上使用约定集合的 AddBefore 和 AddAfter 方法。</span><span class="sxs-lookup"><span data-stu-id="1b52b-198">To do this you can use the AddBefore and AddAfter methods of the Conventions collection on your derived DbContext.</span></span> <span data-ttu-id="1b52b-199">下面的代码将添加前面创建的约定类，使其在内置密钥发现约定之前运行。</span><span class="sxs-lookup"><span data-stu-id="1b52b-199">The following code would add the convention class we created earlier so that it will run before the built in key discovery convention.</span></span>

``` csharp
    modelBuilder.Conventions.AddBefore<IdKeyDiscoveryConvention>(new DateTime2Convention());
```

<span data-ttu-id="1b52b-200">添加需要在内置约定之前或之后运行的约定时，这将是最常使用的，可在以下位置找到内置约定列表： [ModelConfiguration 命名空间](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx)。</span><span class="sxs-lookup"><span data-stu-id="1b52b-200">This is going to be of the most use when adding conventions that need to run before or after the built in conventions, a list of the built in conventions can be found here: [System.Data.Entity.ModelConfiguration.Conventions Namespace](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span>

<span data-ttu-id="1b52b-201">还可以删除不希望应用于模型的约定。</span><span class="sxs-lookup"><span data-stu-id="1b52b-201">You can also remove conventions that you do not want applied to your model.</span></span> <span data-ttu-id="1b52b-202">若要删除约定，请使用 Remove 方法。</span><span class="sxs-lookup"><span data-stu-id="1b52b-202">To remove a convention, use the Remove method.</span></span> <span data-ttu-id="1b52b-203">下面是删除 PluralizingTableNameConvention 的示例。</span><span class="sxs-lookup"><span data-stu-id="1b52b-203">Here is an example of removing the PluralizingTableNameConvention.</span></span>

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
```
