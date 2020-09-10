---
title: 使用 DbContext-EF6
description: 使用实体框架6中的 DbContext
author: divega
ms.date: 10/23/2016
ms.assetid: b0e6bddc-8a87-4d51-b1cb-7756df938c23
uid: ef6/fundamentals/working-with-dbcontext
ms.openlocfilehash: 7845d401cb0b8910cbfbba80eca2fd098c051b7d
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618223"
---
# <a name="working-with-dbcontext"></a><span data-ttu-id="90b92-103">使用 DbContext</span><span class="sxs-lookup"><span data-stu-id="90b92-103">Working with DbContext</span></span>

<span data-ttu-id="90b92-104">若要使用实体框架来使用 .NET 对象查询、插入、更新和删除数据，您首先需要 [创建一个模型](xref:ef6/modeling/index) ，该模型将模型中定义的实体和关系映射到数据库中的表。</span><span class="sxs-lookup"><span data-stu-id="90b92-104">In order to use Entity Framework to query, insert, update, and delete data using .NET objects, you first need to [Create a Model](xref:ef6/modeling/index) which maps the entities and relationships that are defined in your model to tables in a database.</span></span>

<span data-ttu-id="90b92-105">有了模型后，应用程序与之交互的主类 `System.Data.Entity.DbContext` (通常称为上下文类) 。</span><span class="sxs-lookup"><span data-stu-id="90b92-105">Once you have a model, the primary class your application interacts with is `System.Data.Entity.DbContext` (often referred to as the context class).</span></span> <span data-ttu-id="90b92-106">您可以使用与模型关联的 DbContext 来执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="90b92-106">You can use a DbContext associated to a model to:</span></span>
- <span data-ttu-id="90b92-107">编写和执行查询</span><span class="sxs-lookup"><span data-stu-id="90b92-107">Write and execute queries</span></span>   
- <span data-ttu-id="90b92-108">将查询结果具体化为实体对象</span><span class="sxs-lookup"><span data-stu-id="90b92-108">Materialize query results as entity objects</span></span>
- <span data-ttu-id="90b92-109">跟踪对这些对象进行的更改</span><span class="sxs-lookup"><span data-stu-id="90b92-109">Track changes that are made to those objects</span></span>
- <span data-ttu-id="90b92-110">将对象更改保存回数据库</span><span class="sxs-lookup"><span data-stu-id="90b92-110">Persist object changes back on the database</span></span>
- <span data-ttu-id="90b92-111">将内存中的对象绑定到 UI 控件</span><span class="sxs-lookup"><span data-stu-id="90b92-111">Bind objects in memory to UI controls</span></span>

<span data-ttu-id="90b92-112">本页提供有关如何管理上下文类的一些指导。</span><span class="sxs-lookup"><span data-stu-id="90b92-112">This page gives some guidance on how to manage the context class.</span></span>  

## <a name="defining-a-dbcontext-derived-class"></a><span data-ttu-id="90b92-113">定义 DbContext 派生类</span><span class="sxs-lookup"><span data-stu-id="90b92-113">Defining a DbContext derived class</span></span>  

<span data-ttu-id="90b92-114">使用上下文的建议方法是定义从 DbContext 派生的类，并公开 DbSet 属性，这些属性表示上下文中指定实体的集合。</span><span class="sxs-lookup"><span data-stu-id="90b92-114">The recommended way to work with context is to define a class that derives from DbContext and exposes DbSet properties that represent collections of the specified entities in the context.</span></span> <span data-ttu-id="90b92-115">如果使用的是 EF 设计器，则会为您生成上下文。</span><span class="sxs-lookup"><span data-stu-id="90b92-115">If you are working with the EF Designer, the context will be generated for you.</span></span> <span data-ttu-id="90b92-116">如果使用 Code First，则通常会自行编写上下文。</span><span class="sxs-lookup"><span data-stu-id="90b92-116">If you are working with Code First, you will typically write the context yourself.</span></span>  

``` csharp
public class ProductContext : DbContext
{
    public DbSet<Category> Categories { get; set; }
    public DbSet<Product> Products { get; set; }
}
```  

<span data-ttu-id="90b92-117">有了上下文后，您就可以使用或方法来查询、添加 (， `Add` `Attach` ) 或) 使用 `Remove` 这些属性在上下文中删除 (。</span><span class="sxs-lookup"><span data-stu-id="90b92-117">Once you have a context, you would query for, add (using `Add` or `Attach` methods ) or remove (using `Remove`) entities in the context through these properties.</span></span> <span data-ttu-id="90b92-118">访问 `DbSet` 上下文对象上的属性表示一个启动查询，该查询返回指定类型的所有实体。</span><span class="sxs-lookup"><span data-stu-id="90b92-118">Accessing a `DbSet` property on a context object represent a starting query that returns all entities of the specified type.</span></span> <span data-ttu-id="90b92-119">请注意，仅访问属性不会执行查询。</span><span class="sxs-lookup"><span data-stu-id="90b92-119">Note that just accessing a property will not execute the query.</span></span> <span data-ttu-id="90b92-120">执行以下操作时执行查询：</span><span class="sxs-lookup"><span data-stu-id="90b92-120">A query is executed when:</span></span>  

- <span data-ttu-id="90b92-121">`foreach` (C#) 或 `For Each` (Visual Basic) 语句枚举对象。</span><span class="sxs-lookup"><span data-stu-id="90b92-121">It is enumerated by a `foreach` (C#) or `For Each` (Visual Basic) statement.</span></span>  
- <span data-ttu-id="90b92-122">它通过集合操作（如、或）进行枚举 `ToArray` `ToDictionary` `ToList` 。</span><span class="sxs-lookup"><span data-stu-id="90b92-122">It is enumerated by a collection operation such as `ToArray`, `ToDictionary`, or `ToList`.</span></span>  
- <span data-ttu-id="90b92-123">`First` `Any` 在查询的最外面部分指定 LINQ 运算符（如或）。</span><span class="sxs-lookup"><span data-stu-id="90b92-123">LINQ operators such as `First` or `Any` are specified in the outermost part of the query.</span></span>  
- <span data-ttu-id="90b92-124">`Load` `DbEntityEntry.Reload` `Database.ExecuteSqlCommand` 如果在 `DbSet<T>.Find` 上下文中未找到具有指定键的实体，则调用以下方法之一：扩展方法、、和。</span><span class="sxs-lookup"><span data-stu-id="90b92-124">One of the following methods are called: the `Load` extension method, `DbEntityEntry.Reload`,  `Database.ExecuteSqlCommand`, and `DbSet<T>.Find`, if an entity with the specified key is not found already loaded in the context.</span></span>  

## <a name="lifetime"></a><span data-ttu-id="90b92-125">生存期</span><span class="sxs-lookup"><span data-stu-id="90b92-125">Lifetime</span></span>  

<span data-ttu-id="90b92-126">上下文的生存期在实例创建时开始，并在实例被释放或被回收时结束。</span><span class="sxs-lookup"><span data-stu-id="90b92-126">The lifetime of the context begins when the instance is created and ends when the instance is either disposed or garbage-collected.</span></span> <span data-ttu-id="90b92-127">如果希望在块的末尾释放上下文控制的所有资源，**请使用。**</span><span class="sxs-lookup"><span data-stu-id="90b92-127">Use **using** if you want all the resources that the context controls to be disposed at the end of the block.</span></span> <span data-ttu-id="90b92-128">**使用时，编译器**会自动创建 try/finally 块并在**finally**块中调用 dispose。</span><span class="sxs-lookup"><span data-stu-id="90b92-128">When you use **using**, the compiler automatically creates a try/finally block and calls dispose in the **finally** block.</span></span>  

``` csharp
public void UseProducts()
{
    using (var context = new ProductContext())
    {     
        // Perform data access using the context
    }
}
```  

<span data-ttu-id="90b92-129">下面是决定上下文生存期时的一些一般指导原则：</span><span class="sxs-lookup"><span data-stu-id="90b92-129">Here are some general guidelines when deciding on the lifetime of the context:</span></span>  

- <span data-ttu-id="90b92-130">使用 Web 应用程序时，请为每个请求使用上下文实例。</span><span class="sxs-lookup"><span data-stu-id="90b92-130">When working with Web applications, use a context instance per request.</span></span>  
- <span data-ttu-id="90b92-131">使用 Windows Presentation Foundation (WPF) 或 Windows 窗体时，请在每个窗体中使用上下文实例。</span><span class="sxs-lookup"><span data-stu-id="90b92-131">When working with Windows Presentation Foundation (WPF) or Windows Forms, use a context instance per form.</span></span> <span data-ttu-id="90b92-132">这使你可以使用上下文提供的更改跟踪功能。</span><span class="sxs-lookup"><span data-stu-id="90b92-132">This lets you use change-tracking functionality that context provides.</span></span>  
- <span data-ttu-id="90b92-133">如果上下文实例是由依赖关系注入容器创建的，则该容器通常负责释放上下文。</span><span class="sxs-lookup"><span data-stu-id="90b92-133">If the context instance is created by a dependency injection container, it is usually the responsibility of the container to dispose the context.</span></span>
- <span data-ttu-id="90b92-134">如果上下文是在应用程序代码中创建的，请记得在不再需要时释放上下文。</span><span class="sxs-lookup"><span data-stu-id="90b92-134">If the context is created in application code, remember to dispose of the context when it is no longer required.</span></span>  
- <span data-ttu-id="90b92-135">使用长时间运行的上下文时，请考虑以下事项：</span><span class="sxs-lookup"><span data-stu-id="90b92-135">When working with long-running context consider the following:</span></span>  
    - <span data-ttu-id="90b92-136">在将更多对象及其引用加载到内存中时，上下文的内存消耗可能会迅速增加。</span><span class="sxs-lookup"><span data-stu-id="90b92-136">As you load more objects and their references into memory, the memory consumption of the context may increase rapidly.</span></span> <span data-ttu-id="90b92-137">这可能会导致性能问题。</span><span class="sxs-lookup"><span data-stu-id="90b92-137">This may cause performance issues.</span></span>  
    - <span data-ttu-id="90b92-138">上下文不是线程安全的，因此不应在多个线程上同时对其执行工作。</span><span class="sxs-lookup"><span data-stu-id="90b92-138">The context is not thread-safe, therefore it should not be shared across multiple threads doing work on it concurrently.</span></span>
    - <span data-ttu-id="90b92-139">如果异常导致上下文处于不可恢复的状态，则整个应用程序可能会终止。</span><span class="sxs-lookup"><span data-stu-id="90b92-139">If an exception causes the context to be in an unrecoverable state, the whole application may terminate.</span></span>  
    - <span data-ttu-id="90b92-140">随着查询数据的时间和更新数据的时间的差距增大，出现与并发性相关的问题的可能性将会增加。</span><span class="sxs-lookup"><span data-stu-id="90b92-140">The chances of running into concurrency-related issues increase as the gap between the time when the data is queried and updated grows.</span></span>  

## <a name="connections"></a><span data-ttu-id="90b92-141">连接</span><span class="sxs-lookup"><span data-stu-id="90b92-141">Connections</span></span>  

<span data-ttu-id="90b92-142">默认情况下，上下文管理与数据库的连接。</span><span class="sxs-lookup"><span data-stu-id="90b92-142">By default, the context manages connections to the database.</span></span> <span data-ttu-id="90b92-143">上下文会根据需要打开和关闭连接。</span><span class="sxs-lookup"><span data-stu-id="90b92-143">The context opens and closes connections as needed.</span></span> <span data-ttu-id="90b92-144">例如，上下文打开一个连接来执行查询，然后在处理完所有结果集后关闭连接。</span><span class="sxs-lookup"><span data-stu-id="90b92-144">For example, the context opens a connection to execute a query, and then closes the connection when all the result sets have been processed.</span></span>  

<span data-ttu-id="90b92-145">在某些情况下，您需要加强控制应在哪些情况下打开和关闭连接。</span><span class="sxs-lookup"><span data-stu-id="90b92-145">There are cases when you want to have more control over when the connection opens and closes.</span></span> <span data-ttu-id="90b92-146">例如，使用 SQL Server Compact 时，通常建议在应用程序的生存期内维护单独的数据库打开连接，以提高性能。</span><span class="sxs-lookup"><span data-stu-id="90b92-146">For example, when working with SQL Server Compact, it is often recommended to maintain a separate open connection to the database for the lifetime of the application to improve performance.</span></span> <span data-ttu-id="90b92-147">您可以使用 `Connection` 属性手动管理此过程。</span><span class="sxs-lookup"><span data-stu-id="90b92-147">You can manage this process manually by using the `Connection` property.</span></span>  
