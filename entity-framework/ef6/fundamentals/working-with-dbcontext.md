---
title: 使用 DbContext-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: b0e6bddc-8a87-4d51-b1cb-7756df938c23
ms.openlocfilehash: d961ffd8bed7f5b2f82dcfa30fc0241b7437be50
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413881"
---
# <a name="working-with-dbcontext"></a><span data-ttu-id="89d87-102">使用 DbContext</span><span class="sxs-lookup"><span data-stu-id="89d87-102">Working with DbContext</span></span>

<span data-ttu-id="89d87-103">若要使用实体框架来使用 .NET 对象查询、插入、更新和删除数据，您首先需要[创建一个模型](~/ef6/modeling/index.md)，该模型将模型中定义的实体和关系映射到数据库中的表。</span><span class="sxs-lookup"><span data-stu-id="89d87-103">In order to use Entity Framework to query, insert, update, and delete data using .NET objects, you first need to [Create a Model](~/ef6/modeling/index.md) which maps the entities and relationships that are defined in your model to tables in a database.</span></span>

<span data-ttu-id="89d87-104">有了模型后，应用程序与之交互的主类就 `System.Data.Entity.DbContext` （通常称为上下文类）。</span><span class="sxs-lookup"><span data-stu-id="89d87-104">Once you have a model, the primary class your application interacts with is `System.Data.Entity.DbContext` (often referred to as the context class).</span></span> <span data-ttu-id="89d87-105">您可以使用与模型关联的 DbContext 来执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="89d87-105">You can use a DbContext associated to a model to:</span></span>
- <span data-ttu-id="89d87-106">编写和执行查询</span><span class="sxs-lookup"><span data-stu-id="89d87-106">Write and execute queries</span></span>   
- <span data-ttu-id="89d87-107">将查询结果具体化为实体对象</span><span class="sxs-lookup"><span data-stu-id="89d87-107">Materialize query results as entity objects</span></span>
- <span data-ttu-id="89d87-108">跟踪对这些对象进行的更改</span><span class="sxs-lookup"><span data-stu-id="89d87-108">Track changes that are made to those objects</span></span>
- <span data-ttu-id="89d87-109">将对象更改保存回数据库</span><span class="sxs-lookup"><span data-stu-id="89d87-109">Persist object changes back on the database</span></span>
- <span data-ttu-id="89d87-110">将内存中的对象绑定到 UI 控件</span><span class="sxs-lookup"><span data-stu-id="89d87-110">Bind objects in memory to UI controls</span></span>

<span data-ttu-id="89d87-111">本页提供有关如何管理上下文类的一些指导。</span><span class="sxs-lookup"><span data-stu-id="89d87-111">This page gives some guidance on how to manage the context class.</span></span>  

## <a name="defining-a-dbcontext-derived-class"></a><span data-ttu-id="89d87-112">定义 DbContext 派生类</span><span class="sxs-lookup"><span data-stu-id="89d87-112">Defining a DbContext derived class</span></span>  

<span data-ttu-id="89d87-113">使用上下文的建议方法是定义从 DbContext 派生的类，并公开 DbSet 属性，这些属性表示上下文中指定实体的集合。</span><span class="sxs-lookup"><span data-stu-id="89d87-113">The recommended way to work with context is to define a class that derives from DbContext and exposes DbSet properties that represent collections of the specified entities in the context.</span></span> <span data-ttu-id="89d87-114">如果使用的是 EF 设计器，则会为您生成上下文。</span><span class="sxs-lookup"><span data-stu-id="89d87-114">If you are working with the EF Designer, the context will be generated for you.</span></span> <span data-ttu-id="89d87-115">如果使用 Code First，则通常会自行编写上下文。</span><span class="sxs-lookup"><span data-stu-id="89d87-115">If you are working with Code First, you will typically write the context yourself.</span></span>  

``` csharp
public class ProductContext : DbContext
{
    public DbSet<Category> Categories { get; set; }
    public DbSet<Product> Products { get; set; }
}
```  

<span data-ttu-id="89d87-116">拥有上下文后，可以通过这些属性查询、添加（使用 `Add` 或 `Attach` 方法）或在上下文中删除（使用 `Remove`）实体。</span><span class="sxs-lookup"><span data-stu-id="89d87-116">Once you have a context, you would query for, add (using `Add` or `Attach` methods ) or remove (using `Remove`) entities in the context through these properties.</span></span> <span data-ttu-id="89d87-117">访问上下文对象上的 `DbSet` 属性表示一个启动查询，该查询返回指定类型的所有实体。</span><span class="sxs-lookup"><span data-stu-id="89d87-117">Accessing a `DbSet` property on a context object represent a starting query that returns all entities of the specified type.</span></span> <span data-ttu-id="89d87-118">请注意，仅访问属性不会执行查询。</span><span class="sxs-lookup"><span data-stu-id="89d87-118">Note that just accessing a property will not execute the query.</span></span> <span data-ttu-id="89d87-119">执行以下操作时执行查询：</span><span class="sxs-lookup"><span data-stu-id="89d87-119">A query is executed when:</span></span>  

- <span data-ttu-id="89d87-120">`foreach` (C#) 或 `For Each` (Visual Basic) 语句枚举对象。</span><span class="sxs-lookup"><span data-stu-id="89d87-120">It is enumerated by a `foreach` (C#) or `For Each` (Visual Basic) statement.</span></span>  
- <span data-ttu-id="89d87-121">它通过集合操作（如 `ToArray`、`ToDictionary`或 `ToList`）进行枚举。</span><span class="sxs-lookup"><span data-stu-id="89d87-121">It is enumerated by a collection operation such as `ToArray`, `ToDictionary`, or `ToList`.</span></span>  
- <span data-ttu-id="89d87-122">在查询的最外面部分指定 LINQ 运算符，例如 `First` 或 `Any`。</span><span class="sxs-lookup"><span data-stu-id="89d87-122">LINQ operators such as `First` or `Any` are specified in the outermost part of the query.</span></span>  
- <span data-ttu-id="89d87-123">如果在上下文中未找到具有指定键的实体，则调用以下方法之一： `Load` 扩展方法 `DbEntityEntry.Reload`、`Database.ExecuteSqlCommand`和 `DbSet<T>.Find`。</span><span class="sxs-lookup"><span data-stu-id="89d87-123">One of the following methods are called: the `Load` extension method, `DbEntityEntry.Reload`,  `Database.ExecuteSqlCommand`, and `DbSet<T>.Find`, if an entity with the specified key is not found already loaded in the context.</span></span>  

## <a name="lifetime"></a><span data-ttu-id="89d87-124">生存期</span><span class="sxs-lookup"><span data-stu-id="89d87-124">Lifetime</span></span>  

<span data-ttu-id="89d87-125">上下文的生存期在实例创建时开始，并在实例被释放或被回收时结束。</span><span class="sxs-lookup"><span data-stu-id="89d87-125">The lifetime of the context begins when the instance is created and ends when the instance is either disposed or garbage-collected.</span></span> <span data-ttu-id="89d87-126">如果希望在块的末尾释放上下文控制的所有资源，**请使用。**</span><span class="sxs-lookup"><span data-stu-id="89d87-126">Use **using** if you want all the resources that the context controls to be disposed at the end of the block.</span></span> <span data-ttu-id="89d87-127">**使用时，编译器**会自动创建 try/finally 块并在**finally**块中调用 dispose。</span><span class="sxs-lookup"><span data-stu-id="89d87-127">When you use **using**, the compiler automatically creates a try/finally block and calls dispose in the **finally** block.</span></span>  

``` csharp
public void UseProducts()
{
    using (var context = new ProductContext())
    {     
        // Perform data access using the context
    }
}
```  

<span data-ttu-id="89d87-128">下面是决定上下文生存期时的一些一般指导原则：</span><span class="sxs-lookup"><span data-stu-id="89d87-128">Here are some general guidelines when deciding on the lifetime of the context:</span></span>  

- <span data-ttu-id="89d87-129">使用 Web 应用程序时，请为每个请求使用上下文实例。</span><span class="sxs-lookup"><span data-stu-id="89d87-129">When working with Web applications, use a context instance per request.</span></span>  
- <span data-ttu-id="89d87-130">使用 Windows Presentation Foundation （WPF）或 Windows 窗体时，请使用每个窗体的上下文实例。</span><span class="sxs-lookup"><span data-stu-id="89d87-130">When working with Windows Presentation Foundation (WPF) or Windows Forms, use a context instance per form.</span></span> <span data-ttu-id="89d87-131">这使你可以使用上下文提供的更改跟踪功能。</span><span class="sxs-lookup"><span data-stu-id="89d87-131">This lets you use change-tracking functionality that context provides.</span></span>  
- <span data-ttu-id="89d87-132">如果上下文实例是由依赖关系注入容器创建的，则该容器通常负责释放上下文。</span><span class="sxs-lookup"><span data-stu-id="89d87-132">If the context instance is created by a dependency injection container, it is usually the responsibility of the container to dispose the context.</span></span>
- <span data-ttu-id="89d87-133">如果上下文是在应用程序代码中创建的，请记得在不再需要时释放上下文。</span><span class="sxs-lookup"><span data-stu-id="89d87-133">If the context is created in application code, remember to dispose of the context when it is no longer required.</span></span>  
- <span data-ttu-id="89d87-134">使用长时间运行的上下文时，请考虑以下事项：</span><span class="sxs-lookup"><span data-stu-id="89d87-134">When working with long-running context consider the following:</span></span>  
    - <span data-ttu-id="89d87-135">在将更多对象及其引用加载到内存中时，上下文的内存消耗可能会迅速增加。</span><span class="sxs-lookup"><span data-stu-id="89d87-135">As you load more objects and their references into memory, the memory consumption of the context may increase rapidly.</span></span> <span data-ttu-id="89d87-136">这可能会导致性能问题。</span><span class="sxs-lookup"><span data-stu-id="89d87-136">This may cause performance issues.</span></span>  
    - <span data-ttu-id="89d87-137">上下文不是线程安全的，因此不应在多个线程上同时对其执行工作。</span><span class="sxs-lookup"><span data-stu-id="89d87-137">The context is not thread-safe, therefore it should not be shared across multiple threads doing work on it concurrently.</span></span>
    - <span data-ttu-id="89d87-138">如果异常导致上下文处于不可恢复的状态，则整个应用程序可能会终止。</span><span class="sxs-lookup"><span data-stu-id="89d87-138">If an exception causes the context to be in an unrecoverable state, the whole application may terminate.</span></span>  
    - <span data-ttu-id="89d87-139">随着查询数据的时间和更新数据的时间的差距增大，出现与并发性相关的问题的可能性将会增加。</span><span class="sxs-lookup"><span data-stu-id="89d87-139">The chances of running into concurrency-related issues increase as the gap between the time when the data is queried and updated grows.</span></span>  

## <a name="connections"></a><span data-ttu-id="89d87-140">连接</span><span class="sxs-lookup"><span data-stu-id="89d87-140">Connections</span></span>  

<span data-ttu-id="89d87-141">默认情况下，上下文管理与数据库的连接。</span><span class="sxs-lookup"><span data-stu-id="89d87-141">By default, the context manages connections to the database.</span></span> <span data-ttu-id="89d87-142">上下文会根据需要打开和关闭连接。</span><span class="sxs-lookup"><span data-stu-id="89d87-142">The context opens and closes connections as needed.</span></span> <span data-ttu-id="89d87-143">例如，上下文打开一个连接来执行查询，然后在处理完所有结果集后关闭连接。</span><span class="sxs-lookup"><span data-stu-id="89d87-143">For example, the context opens a connection to execute a query, and then closes the connection when all the result sets have been processed.</span></span>  

<span data-ttu-id="89d87-144">在某些情况下，您需要加强控制应在哪些情况下打开和关闭连接。</span><span class="sxs-lookup"><span data-stu-id="89d87-144">There are cases when you want to have more control over when the connection opens and closes.</span></span> <span data-ttu-id="89d87-145">例如，使用 SQL Server Compact 时，通常建议在应用程序的生存期内维护单独的数据库打开连接，以提高性能。</span><span class="sxs-lookup"><span data-stu-id="89d87-145">For example, when working with SQL Server Compact, it is often recommended to maintain a separate open connection to the database for the lifetime of the application to improve performance.</span></span> <span data-ttu-id="89d87-146">您可以使用 `Connection` 属性手动管理此过程。</span><span class="sxs-lookup"><span data-stu-id="89d87-146">You can manage this process manually by using the `Connection` property.</span></span>  
