---
title: 使用属性值的 EF6
author: divega
ms.date: 10/23/2016
ms.assetid: e3278b4b-9378-4fdb-923d-f64d80aaae70
ms.openlocfilehash: afde503bb4ed15fcf83a57053541cd5da8c89835
ms.sourcegitcommit: 50521b4a2f71139e6a7210a69ac73da582ef46cf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2019
ms.locfileid: "67416678"
---
# <a name="working-with-property-values"></a><span data-ttu-id="86f50-102">使用属性值</span><span class="sxs-lookup"><span data-stu-id="86f50-102">Working with property values</span></span>
<span data-ttu-id="86f50-103">大多数情况下 Entity Framework 将负责跟踪状态、 原始值和当前值的实体实例的属性。</span><span class="sxs-lookup"><span data-stu-id="86f50-103">For the most part Entity Framework will take care of tracking the state, original values, and current values of the properties of your entity instances.</span></span> <span data-ttu-id="86f50-104">但是，可能有某些情况下-例如断开连接的场景-你想要查看或操作的属性信息 EF 拥有。</span><span class="sxs-lookup"><span data-stu-id="86f50-104">However, there may be some cases - such as disconnected scenarios - where you want to view or manipulate the information EF has about the properties.</span></span> <span data-ttu-id="86f50-105">本主题所介绍的方法同样适用于查询使用 Code First 和 EF 设计器创建的模型。</span><span class="sxs-lookup"><span data-stu-id="86f50-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="86f50-106">实体框架会跟踪的跟踪实体的每个属性的两个值。</span><span class="sxs-lookup"><span data-stu-id="86f50-106">Entity Framework keeps track of two values for each property of a tracked entity.</span></span> <span data-ttu-id="86f50-107">当前值是，顾名思义，实体中属性的当前值。</span><span class="sxs-lookup"><span data-stu-id="86f50-107">The current value is, as the name indicates, the current value of the property in the entity.</span></span> <span data-ttu-id="86f50-108">如果实体已从数据库中查询或附加到上下文，该属性具有值的原始值。</span><span class="sxs-lookup"><span data-stu-id="86f50-108">The original value is the value that the property had when the entity was queried from the database or attached to the context.</span></span>  

<span data-ttu-id="86f50-109">有两种常规机制，用于处理属性值：</span><span class="sxs-lookup"><span data-stu-id="86f50-109">There are two general mechanisms for working with property values:</span></span>  

- <span data-ttu-id="86f50-110">可以使用属性方法以强类型化方式获取的单个属性的值。</span><span class="sxs-lookup"><span data-stu-id="86f50-110">The value of a single property can be obtained in a strongly typed way using the Property method.</span></span>  
- <span data-ttu-id="86f50-111">实体的所有属性的值可以读入 DbPropertyValues 对象。</span><span class="sxs-lookup"><span data-stu-id="86f50-111">Values for all properties of an entity can be read into a DbPropertyValues object.</span></span> <span data-ttu-id="86f50-112">DbPropertyValues 然后充当类似于字典的对象，以允许读取和设置属性值。</span><span class="sxs-lookup"><span data-stu-id="86f50-112">DbPropertyValues then acts as a dictionary-like object to allow property values to be read and set.</span></span> <span data-ttu-id="86f50-113">可以从另一个 DbPropertyValues 对象中的值或其他某个对象，如实体或简单的数据传输对象 (DTO) 的另一个副本中的值设置 DbPropertyValues 对象中的值。</span><span class="sxs-lookup"><span data-stu-id="86f50-113">The values in a DbPropertyValues object can be set from values in another DbPropertyValues object or from values in some other object, such as another copy of the entity or a simple data transfer object (DTO).</span></span>  

<span data-ttu-id="86f50-114">以下部分说明了使用这两个以上的机制的示例。</span><span class="sxs-lookup"><span data-stu-id="86f50-114">The sections below show examples of using both of the above mechanisms.</span></span>  

## <a name="getting-and-setting-the-current-or-original-value-of-an-individual-property"></a><span data-ttu-id="86f50-115">获取和设置的个别属性的当前或原始值</span><span class="sxs-lookup"><span data-stu-id="86f50-115">Getting and setting the current or original value of an individual property</span></span>  

<span data-ttu-id="86f50-116">下面的示例说明如何可以读取，然后将设置为新值的属性的当前值：</span><span class="sxs-lookup"><span data-stu-id="86f50-116">The example below shows how the current value of a property can be read and then set to a new value:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(3);

    // Read the current value of the Name property
    string currentName1 = context.Entry(blog).Property(u => u.Name).CurrentValue;

    // Set the Name property to a new value
    context.Entry(blog).Property(u => u.Name).CurrentValue = "My Fancy Blog";

    // Read the current value of the Name property using a string for the property name
    object currentName2 = context.Entry(blog).Property("Name").CurrentValue;

    // Set the Name property to a new value using a string for the property name
    context.Entry(blog).Property("Name").CurrentValue = "My Boring Blog";
}
```  

<span data-ttu-id="86f50-117">使用而不是 CurrentValue 属性的 OriginalValue 属性来读取或设置原始值。</span><span class="sxs-lookup"><span data-stu-id="86f50-117">Use the OriginalValue property instead of the CurrentValue property to read or set the original value.</span></span>  

<span data-ttu-id="86f50-118">请注意，返回的值的类型为"对象"用于指定属性名称的字符串。</span><span class="sxs-lookup"><span data-stu-id="86f50-118">Note that the returned value is typed as “object” when a string is used to specify the property name.</span></span> <span data-ttu-id="86f50-119">但是，强类型返回的值，如果使用了 lambda 表达式。</span><span class="sxs-lookup"><span data-stu-id="86f50-119">On the other hand, the returned value is strongly typed if a lambda expression is used.</span></span>  

<span data-ttu-id="86f50-120">设置此类的属性值将仅将该属性标记为已修改的新值是否不同于旧值。</span><span class="sxs-lookup"><span data-stu-id="86f50-120">Setting the property value like this will only mark the property as modified if the new value is different from the old value.</span></span>  

<span data-ttu-id="86f50-121">以这种方式设置属性值可以自动检测到更改即使 AutoDetectChanges 处于关闭状态。</span><span class="sxs-lookup"><span data-stu-id="86f50-121">When a property value is set in this way the change is automatically detected even if AutoDetectChanges is turned off.</span></span>  

## <a name="getting-and-setting-the-current-value-of-an-unmapped-property"></a><span data-ttu-id="86f50-122">获取和设置未映射属性的当前值</span><span class="sxs-lookup"><span data-stu-id="86f50-122">Getting and setting the current value of an unmapped property</span></span>  

<span data-ttu-id="86f50-123">此外可以阅读不映射到数据库的属性的当前值。</span><span class="sxs-lookup"><span data-stu-id="86f50-123">The current value of a property that is not mapped to the database can also be read.</span></span> <span data-ttu-id="86f50-124">未映射属性的一个示例可能是博客上的 RssLink 属性。</span><span class="sxs-lookup"><span data-stu-id="86f50-124">An example of an unmapped property could be an RssLink property on Blog.</span></span> <span data-ttu-id="86f50-125">此值可能根据 BlogId，计算，因此不需要存储在数据库中。</span><span class="sxs-lookup"><span data-stu-id="86f50-125">This value may be calculated based on the BlogId, and therefore doesn't need to be stored in the database.</span></span> <span data-ttu-id="86f50-126">例如：</span><span class="sxs-lookup"><span data-stu-id="86f50-126">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    // Read the current value of an unmapped property
    var rssLink = context.Entry(blog).Property(p => p.RssLink).CurrentValue;

    // Use a string to specify the property name
    var rssLinkAgain = context.Entry(blog).Property("RssLink").CurrentValue;
}
```  

<span data-ttu-id="86f50-127">如果该属性公开 setter，还可以设置的当前值。</span><span class="sxs-lookup"><span data-stu-id="86f50-127">The current value can also be set if the property exposes a setter.</span></span>  

<span data-ttu-id="86f50-128">执行未映射属性的实体框架验证时，读取未映射的属性的值非常有用。</span><span class="sxs-lookup"><span data-stu-id="86f50-128">Reading the values of unmapped properties is useful when performing Entity Framework validation of unmapped properties.</span></span> <span data-ttu-id="86f50-129">出于同一原因可以读取和设置的属性不当前正在由上下文跟踪的实体的当前值。</span><span class="sxs-lookup"><span data-stu-id="86f50-129">For the same reason current values can be read and set for properties of entities that are not currently being tracked by the context.</span></span> <span data-ttu-id="86f50-130">例如：</span><span class="sxs-lookup"><span data-stu-id="86f50-130">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Create an entity that is not being tracked
    var blog = new Blog { Name = "ADO.NET Blog" };

    // Read and set the current value of Name as before
    var currentName1 = context.Entry(blog).Property(u => u.Name).CurrentValue;
    context.Entry(blog).Property(u => u.Name).CurrentValue = "My Fancy Blog";
    var currentName2 = context.Entry(blog).Property("Name").CurrentValue;
    context.Entry(blog).Property("Name").CurrentValue = "My Boring Blog";
}
```  

<span data-ttu-id="86f50-131">请注意，原始值不能用于未映射的属性或未正在由上下文跟踪的实体的属性。</span><span class="sxs-lookup"><span data-stu-id="86f50-131">Note that original values are not available for unmapped properties or for properties of entities that are not being tracked by the context.</span></span>  

## <a name="checking-whether-a-property-is-marked-as-modified"></a><span data-ttu-id="86f50-132">检查属性标记为已修改</span><span class="sxs-lookup"><span data-stu-id="86f50-132">Checking whether a property is marked as modified</span></span>  

<span data-ttu-id="86f50-133">下面的示例演示如何检查的个别属性标记为已修改：</span><span class="sxs-lookup"><span data-stu-id="86f50-133">The example below shows how to check whether or not an individual property is marked as modified:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var nameIsModified1 = context.Entry(blog).Property(u => u.Name).IsModified;

    // Use a string for the property name
    var nameIsModified2 = context.Entry(blog).Property("Name").IsModified;
}
```  

<span data-ttu-id="86f50-134">当调用 SaveChanges 时，已修改属性的值是作为更新发送到数据库中。</span><span class="sxs-lookup"><span data-stu-id="86f50-134">The values of modified properties are sent as updates to the database when SaveChanges is called.</span></span>  

##  <a name="marking-a-property-as-modified"></a><span data-ttu-id="86f50-135">将标记为已修改的属性</span><span class="sxs-lookup"><span data-stu-id="86f50-135">Marking a property as modified</span></span>  

<span data-ttu-id="86f50-136">下面的示例演示如何强制执行标记为已修改的个别属性：</span><span class="sxs-lookup"><span data-stu-id="86f50-136">The example below shows how to force an individual property to be marked as modified:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    context.Entry(blog).Property(u => u.Name).IsModified = true;

    // Use a string for the property name
    context.Entry(blog).Property("Name").IsModified = true;
}
```  

<span data-ttu-id="86f50-137">将属性标记为已修改强制更新为将发送到该属性的数据库，当调用 SaveChanges 时，即使该属性的当前值等同于其原始值。</span><span class="sxs-lookup"><span data-stu-id="86f50-137">Marking a property as modified forces an update to be send to the database for the property when SaveChanges is called even if the current value of the property is the same as its original value.</span></span>  

<span data-ttu-id="86f50-138">不是目前无法重置后标记为已修改未修改的单个属性。</span><span class="sxs-lookup"><span data-stu-id="86f50-138">It is not currently possible to reset an individual property to be not modified after it has been marked as modified.</span></span> <span data-ttu-id="86f50-139">这是我们计划在将来的版本中支持。</span><span class="sxs-lookup"><span data-stu-id="86f50-139">This is something we plan to support in a future release.</span></span>  

## <a name="reading-current-original-and-database-values-for-all-properties-of-an-entity"></a><span data-ttu-id="86f50-140">读取当前、 原始值和所有属性的实体的数据库值</span><span class="sxs-lookup"><span data-stu-id="86f50-140">Reading current, original, and database values for all properties of an entity</span></span>  

<span data-ttu-id="86f50-141">下面的示例演示如何读取的当前值、 原始值，以及数据库中的所有映射属性的实体的数据库的实际值。</span><span class="sxs-lookup"><span data-stu-id="86f50-141">The example below shows how to read the current values, the original values, and the values actually in the database for all mapped properties of an entity.</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Make a modification to Name in the tracked entity
    blog.Name = "My Cool Blog";

    // Make a modification to Name in the database
    context.Database.SqlCommand("update dbo.Blogs set Name = 'My Boring Blog' where Id = 1");

    // Print out current, original, and database values
    Console.WriteLine("Current values:");
    PrintValues(context.Entry(blog).CurrentValues);

    Console.WriteLine("\nOriginal values:");
    PrintValues(context.Entry(blog).OriginalValues);

    Console.WriteLine("\nDatabase values:");
    PrintValues(context.Entry(blog).GetDatabaseValues());
}

public static void PrintValues(DbPropertyValues values)
{
    foreach (var propertyName in values.PropertyNames)
    {
        Console.WriteLine("Property {0} has value {1}",
                          propertyName, values[propertyName]);
    }
}
```  

<span data-ttu-id="86f50-142">当前值是实体的当前包含的属性的值。</span><span class="sxs-lookup"><span data-stu-id="86f50-142">The current values are the values that the properties of the entity currently contain.</span></span> <span data-ttu-id="86f50-143">原始值是该实体已查询时从数据库读取的值。</span><span class="sxs-lookup"><span data-stu-id="86f50-143">The original values are the values that were read from the database when the entity was queried.</span></span> <span data-ttu-id="86f50-144">数据库值是当前数据库中存储的值。</span><span class="sxs-lookup"><span data-stu-id="86f50-144">The database values are the values as they are currently stored in the database.</span></span> <span data-ttu-id="86f50-145">在数据库中的值可能已更改，因为如时并发编辑，以另一个用户已发出对数据库进行了查询实体时，获取数据库值很有用。</span><span class="sxs-lookup"><span data-stu-id="86f50-145">Getting the database values is useful when the values in the database may have changed since the entity was queried such as when a concurrent edit to the database has been made by another user.</span></span>  

## <a name="setting-current-or-original-values-from-another-object"></a><span data-ttu-id="86f50-146">从另一个对象设置当前或原始值</span><span class="sxs-lookup"><span data-stu-id="86f50-146">Setting current or original values from another object</span></span>  

<span data-ttu-id="86f50-147">可以通过从另一个对象复制的值更新被跟踪实体的当前或原始值。</span><span class="sxs-lookup"><span data-stu-id="86f50-147">The current or original values of a tracked entity can be updated by copying values from another object.</span></span> <span data-ttu-id="86f50-148">例如：</span><span class="sxs-lookup"><span data-stu-id="86f50-148">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    var coolBlog = new Blog { Id = 1, Name = "My Cool Blog" };
    var boringBlog = new BlogDto { Id = 1, Name = "My Boring Blog" };

    // Change the current and original values by copying the values from other objects
    var entry = context.Entry(blog);
    entry.CurrentValues.SetValues(coolBlog);
    entry.OriginalValues.SetValues(boringBlog);

    // Print out current and original values
    Console.WriteLine("Current values:");
    PrintValues(entry.CurrentValues);

    Console.WriteLine("\nOriginal values:");
    PrintValues(entry.OriginalValues);
}

public class BlogDto
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```  

<span data-ttu-id="86f50-149">运行上面的代码将打印出：</span><span class="sxs-lookup"><span data-stu-id="86f50-149">Running the code above will print out:</span></span>  

```  
Current values:
Property Id has value 1
Property Name has value My Cool Blog

Original values:
Property Id has value 1
Property Name has value My Boring Blog
```  

<span data-ttu-id="86f50-150">使用从服务调用或 n 层应用程序中的客户端获取的值更新实体时，有时会使用此方法。</span><span class="sxs-lookup"><span data-stu-id="86f50-150">This technique is sometimes used when updating an entity with values obtained from a service call or a client in an n-tier application.</span></span> <span data-ttu-id="86f50-151">请注意，使用对象不必是实体类型相同的只要它具有名称匹配的实体的属性。</span><span class="sxs-lookup"><span data-stu-id="86f50-151">Note that the object used does not have to be of the same type as the entity so long as it has properties whose names match those of the entity.</span></span> <span data-ttu-id="86f50-152">在上面的示例中，BlogDTO 实例用于更新原始值。</span><span class="sxs-lookup"><span data-stu-id="86f50-152">In the example above, an instance of BlogDTO is used to update the original values.</span></span>  

<span data-ttu-id="86f50-153">请注意为不同的值时从另一个对象复制设置的属性标记为已修改。</span><span class="sxs-lookup"><span data-stu-id="86f50-153">Note that only properties that are set to different values when copied from the other object will be marked as modified.</span></span>  

## <a name="setting-current-or-original-values-from-a-dictionary"></a><span data-ttu-id="86f50-154">从字典中设置当前或原始值</span><span class="sxs-lookup"><span data-stu-id="86f50-154">Setting current or original values from a dictionary</span></span>  

<span data-ttu-id="86f50-155">可以通过将值从字典或某些其他数据结构中复制更新被跟踪实体的当前或原始值。</span><span class="sxs-lookup"><span data-stu-id="86f50-155">The current or original values of a tracked entity can be updated by copying values from a dictionary or some other data structure.</span></span> <span data-ttu-id="86f50-156">例如：</span><span class="sxs-lookup"><span data-stu-id="86f50-156">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var newValues = new Dictionary\<string, object>
    {
        { "Name", "The New ADO.NET Blog" },
        { "Url", "blogs.msdn.com/adonet" },
    };

    var currentValues = context.Entry(blog).CurrentValues;

    foreach (var propertyName in newValues.Keys)
    {
        currentValues[propertyName] = newValues[propertyName];
    }

    PrintValues(currentValues);
}
```  

<span data-ttu-id="86f50-157">使用引入了属性而不是当前值属性来设置原始值。</span><span class="sxs-lookup"><span data-stu-id="86f50-157">Use the OriginalValues property instead of the CurrentValues property to set original values.</span></span>  

## <a name="setting-current-or-original-values-from-a-dictionary-using-property"></a><span data-ttu-id="86f50-158">使用属性字典中设置当前或原始值</span><span class="sxs-lookup"><span data-stu-id="86f50-158">Setting current or original values from a dictionary using Property</span></span>  

<span data-ttu-id="86f50-159">使用当前值或引入了如上所示的替代方法是使用属性方法以设置每个属性的值。</span><span class="sxs-lookup"><span data-stu-id="86f50-159">An alternative to using CurrentValues or OriginalValues as shown above is to use the Property method to set the value of each property.</span></span> <span data-ttu-id="86f50-160">当您需要设置复杂属性的值时，这可能是更可取。</span><span class="sxs-lookup"><span data-stu-id="86f50-160">This can be preferable when you need to set the values of complex properties.</span></span> <span data-ttu-id="86f50-161">例如：</span><span class="sxs-lookup"><span data-stu-id="86f50-161">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    var newValues = new Dictionary\<string, object>
    {
        { "Name", "John Doe" },
        { "Location.City", "Redmond" },
        { "Location.State.Name", "Washington" },
        { "Location.State.Code", "WA" },
    };

    var entry = context.Entry(user);

    foreach (var propertyName in newValues.Keys)
    {
        entry.Property(propertyName).CurrentValue = newValues[propertyName];
    }
}
```  

<span data-ttu-id="86f50-162">在上面的复杂属性的示例使用访问包含点的名称。</span><span class="sxs-lookup"><span data-stu-id="86f50-162">In the example above complex properties are accessed using dotted names.</span></span> <span data-ttu-id="86f50-163">有关访问复杂属性的其他方法请参阅后面本主题专门针对复杂属性的两个部分。</span><span class="sxs-lookup"><span data-stu-id="86f50-163">For other ways to access complex properties see the two sections later in this topic specifically about complex properties.</span></span>  

## <a name="creating-a-cloned-object-containing-current-original-or-database-values"></a><span data-ttu-id="86f50-164">创建一个克隆的对象包含当前、 原始路径或数据库值</span><span class="sxs-lookup"><span data-stu-id="86f50-164">Creating a cloned object containing current, original, or database values</span></span>  

<span data-ttu-id="86f50-165">从当前值，引入了，返回的 DbPropertyValues 对象或 GetDatabaseValues 可用于创建实体的克隆。</span><span class="sxs-lookup"><span data-stu-id="86f50-165">The DbPropertyValues object returned from CurrentValues, OriginalValues, or GetDatabaseValues can be used to create a clone of the entity.</span></span> <span data-ttu-id="86f50-166">此克隆将包含从 DbPropertyValues 对象用来创建它的属性值。</span><span class="sxs-lookup"><span data-stu-id="86f50-166">This clone will contain the property values from the DbPropertyValues object used to create it.</span></span> <span data-ttu-id="86f50-167">例如：</span><span class="sxs-lookup"><span data-stu-id="86f50-167">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var clonedBlog = context.Entry(blog).GetDatabaseValues().ToObject();
}
```  

<span data-ttu-id="86f50-168">请注意，返回的对象不是实体，并且未被跟踪上下文。</span><span class="sxs-lookup"><span data-stu-id="86f50-168">Note that the object returned is not the entity and is not being tracked by the context.</span></span> <span data-ttu-id="86f50-169">返回的对象还没有设置为其他对象的任何关系。</span><span class="sxs-lookup"><span data-stu-id="86f50-169">The returned object also does not have any relationships set to other objects.</span></span>  

<span data-ttu-id="86f50-170">克隆的对象可用于解决与并发更新到数据库，尤其是涉及数据绑定到特定类型的对象的用户界面正在使用的位置相关的问题。</span><span class="sxs-lookup"><span data-stu-id="86f50-170">The cloned object can be useful for resolving issues related to concurrent updates to the database, especially where a UI that involves data binding to objects of a certain type is being used.</span></span>  

## <a name="getting-and-setting-the-current-or-original-values-of-complex-properties"></a><span data-ttu-id="86f50-171">获取和设置的复杂属性的当前或原始值</span><span class="sxs-lookup"><span data-stu-id="86f50-171">Getting and setting the current or original values of complex properties</span></span>  

<span data-ttu-id="86f50-172">读取和使用属性方法，就像它可以是基元属性集，可以是整个复杂对象的值。</span><span class="sxs-lookup"><span data-stu-id="86f50-172">The value of an entire complex object can be read and set using the Property method just as it can be for a primitive property.</span></span> <span data-ttu-id="86f50-173">此外，可以向下钻取到复杂的对象，该对象或甚至嵌套的对象的读取或设置属性。</span><span class="sxs-lookup"><span data-stu-id="86f50-173">In addition you can drill down into the complex object and read or set properties of that object, or even a nested object.</span></span> <span data-ttu-id="86f50-174">下面是一些可能的恶意活动：</span><span class="sxs-lookup"><span data-stu-id="86f50-174">Here are some examples:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    // Get the Location complex object
    var location = context.Entry(user)
                       .Property(u => u.Location)
                       .CurrentValue;

    // Get the nested State complex object using chained calls
    var state1 = context.Entry(user)
                     .ComplexProperty(u => u.Location)
                     .Property(l => l.State)
                     .CurrentValue;

    // Get the nested State complex object using a single lambda expression
    var state2 = context.Entry(user)
                     .Property(u => u.Location.State)
                     .CurrentValue;

    // Get the nested State complex object using a dotted string
    var state3 = context.Entry(user)
                     .Property("Location.State")
                     .CurrentValue;

    // Get the value of the Name property on the nested State complex object using chained calls
    var name1 = context.Entry(user)
                       .ComplexProperty(u => u.Location)
                       .ComplexProperty(l => l.State)
                       .Property(s => s.Name)
                       .CurrentValue;

    // Get the value of the Name property on the nested State complex object using a single lambda expression
    var name2 = context.Entry(user)
                       .Property(u => u.Location.State.Name)
                       .CurrentValue;

    // Get the value of the Name property on the nested State complex object using a dotted string
    var name3 = context.Entry(user)
                       .Property("Location.State.Name")
                       .CurrentValue;
}
```  

<span data-ttu-id="86f50-175">使用而不是 CurrentValue 属性的 OriginalValue 属性获取或设置原始值。</span><span class="sxs-lookup"><span data-stu-id="86f50-175">Use the OriginalValue property instead of the CurrentValue property to get or set an original value.</span></span>  

<span data-ttu-id="86f50-176">请注意该属性或 ComplexProperty 方法可用于访问复杂属性。</span><span class="sxs-lookup"><span data-stu-id="86f50-176">Note that either the Property or the ComplexProperty method can be used to access a complex property.</span></span> <span data-ttu-id="86f50-177">但是，如果你想要向下钻取到具有其他属性的复杂对象或调用的 ComplexProperty，则必须使用 ComplexProperty 方法。</span><span class="sxs-lookup"><span data-stu-id="86f50-177">However, the ComplexProperty method must be used if you wish to drill down into the complex object with additional Property or ComplexProperty calls.</span></span>  

## <a name="using-dbpropertyvalues-to-access-complex-properties"></a><span data-ttu-id="86f50-178">使用 DbPropertyValues 访问复杂属性</span><span class="sxs-lookup"><span data-stu-id="86f50-178">Using DbPropertyValues to access complex properties</span></span>  

<span data-ttu-id="86f50-179">当您使用当前值，引入了或 GetDatabaseValues 来获取所有当前原始，或数据库实体值，为嵌套 DbPropertyValues 对象返回的任何复杂属性的值。</span><span class="sxs-lookup"><span data-stu-id="86f50-179">When you use CurrentValues, OriginalValues, or GetDatabaseValues to get all the current, original, or database values for an entity, the values of any complex properties are returned as nested DbPropertyValues objects.</span></span> <span data-ttu-id="86f50-180">这些嵌套对象可以然后使用来获取复杂对象的值。</span><span class="sxs-lookup"><span data-stu-id="86f50-180">These nested objects can then be used to get values of the complex object.</span></span> <span data-ttu-id="86f50-181">例如，以下方法将打印出的所有属性，包括任何复杂属性和嵌套的复杂属性的值的值。</span><span class="sxs-lookup"><span data-stu-id="86f50-181">For example, the following method will print out the values of all properties, including values of any complex properties and nested complex properties.</span></span>  

``` csharp
public static void WritePropertyValues(string parentPropertyName, DbPropertyValues propertyValues)
{
    foreach (var propertyName in propertyValues.PropertyNames)
    {
        var nestedValues = propertyValues[propertyName] as DbPropertyValues;
        if (nestedValues != null)
        {
            WritePropertyValues(parentPropertyName + propertyName + ".", nestedValues);
        }
        else
        {
            Console.WriteLine("Property {0}{1} has value {2}",
                              parentPropertyName, propertyName,
                              propertyValues[propertyName]);
        }
    }
}
```  

<span data-ttu-id="86f50-182">打印出所有当前属性值的方法将调用如下：</span><span class="sxs-lookup"><span data-stu-id="86f50-182">To print out all current property values the method would be called like this:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    WritePropertyValues("", context.Entry(user).CurrentValues);
}
```  
