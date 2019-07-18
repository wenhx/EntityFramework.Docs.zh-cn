---
title: Async query 和 EF6
author: divega
ms.date: 10/23/2016
ms.assetid: d56e6f1d-4bd1-4b50-9558-9a30e04a8ec3
ms.openlocfilehash: bf2039110962e8dd114242dcd0b9454963750774
ms.sourcegitcommit: c9c3e00c2d445b784423469838adc071a946e7c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68306584"
---
# <a name="async-query-and-save"></a><span data-ttu-id="68f87-102">异步查询并保存</span><span class="sxs-lookup"><span data-stu-id="68f87-102">Async query and save</span></span>
> [!NOTE]
> <span data-ttu-id="68f87-103">**仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。</span><span class="sxs-lookup"><span data-stu-id="68f87-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="68f87-104">如果使用的是早期版本，则部分或全部信息不适用。</span><span class="sxs-lookup"><span data-stu-id="68f87-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="68f87-105">EF6 引入了对异步查询的支持, 并使用 .NET 4.5 中引入的[async 和 await 关键字](https://msdn.microsoft.com/library/vstudio/hh191443.aspx)进行保存。</span><span class="sxs-lookup"><span data-stu-id="68f87-105">EF6 introduced support for asynchronous query and save using the [async and await keywords](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) that were introduced in .NET 4.5.</span></span> <span data-ttu-id="68f87-106">虽然并非所有应用程序都可以受益于异步, 但它可用于在处理长时间运行的、网络或 i/o 限制的任务时提高客户端的响应能力和服务器的可伸缩性。</span><span class="sxs-lookup"><span data-stu-id="68f87-106">While not all applications may benefit from asynchrony, it can be used to improve client responsiveness and server scalability when handling long-running, network or I/O-bound tasks.</span></span>

## <a name="when-to-really-use-async"></a><span data-ttu-id="68f87-107">何时真正使用 async</span><span class="sxs-lookup"><span data-stu-id="68f87-107">When to really use async</span></span>

<span data-ttu-id="68f87-108">本演练的目的是以一种可以轻松地观察异步和同步程序执行之间的差异的方式引入异步概念。</span><span class="sxs-lookup"><span data-stu-id="68f87-108">The purpose of this walkthrough is to introduce the async concepts in a way that makes it easy to observe the difference between asynchronous and synchronous program execution.</span></span> <span data-ttu-id="68f87-109">本演练并不旨在说明异步编程提供了好处的关键方案。</span><span class="sxs-lookup"><span data-stu-id="68f87-109">This walkthrough is not intended to illustrate any of the key scenarios where async programming provides benefits.</span></span>

<span data-ttu-id="68f87-110">异步编程主要侧重于释放当前托管线程 (运行 .NET 代码的线程) 来执行其他工作, 同时等待不需要托管线程的任何计算时间的操作。</span><span class="sxs-lookup"><span data-stu-id="68f87-110">Async programming is primarily focused on freeing up the current managed thread (thread running .NET code) to do other work while it waits for an operation that does not require any compute time from a managed thread.</span></span> <span data-ttu-id="68f87-111">例如, 当数据库引擎正在处理查询时, .NET 代码不会执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="68f87-111">For example, whilst the database engine is processing a query there is nothing to be done by .NET code.</span></span>

<span data-ttu-id="68f87-112">在客户端应用程序 (WinForms、WPF 等) 中, 当前线程可用于在执行异步操作时保持 UI 的响应能力。</span><span class="sxs-lookup"><span data-stu-id="68f87-112">In client applications (WinForms, WPF, etc.) the current thread can be used to keep the UI responsive while the async operation is performed.</span></span> <span data-ttu-id="68f87-113">在服务器应用程序 (ASP.NET 等) 中, 线程可用于处理其他传入请求-这可以减少内存使用量和/或提高服务器的吞吐量。</span><span class="sxs-lookup"><span data-stu-id="68f87-113">In server applications (ASP.NET etc.) the thread can be used to process other incoming requests - this can reduce memory usage and/or increase throughput of the server.</span></span>

<span data-ttu-id="68f87-114">在大多数使用 async 的应用程序中, 没有明显的好处, 甚至可能会造成不利影响。</span><span class="sxs-lookup"><span data-stu-id="68f87-114">In most applications using async will have no noticeable benefits and even could be detrimental.</span></span> <span data-ttu-id="68f87-115">在提交到特定方案之前, 请使用测试、分析和常见意义来度量异步的影响。</span><span class="sxs-lookup"><span data-stu-id="68f87-115">Use tests, profiling and common sense to measure the impact of async in your particular scenario before committing to it.</span></span>

<span data-ttu-id="68f87-116">下面是一些用于了解 async 的更多资源:</span><span class="sxs-lookup"><span data-stu-id="68f87-116">Here are some more resources to learn about async:</span></span>

-   [<span data-ttu-id="68f87-117">.NET 4.5 中的 Brandon Bray 概述</span><span class="sxs-lookup"><span data-stu-id="68f87-117">Brandon Bray’s overview of async/await in .NET 4.5</span></span>](https://blogs.msdn.com/b/dotnet/archive/2012/04/03/async-in-4-5-worth-the-await.aspx)
-   <span data-ttu-id="68f87-118">MSDN Library 中的[异步编程](https://msdn.microsoft.com/library/hh191443.aspx)页</span><span class="sxs-lookup"><span data-stu-id="68f87-118">[Asynchronous Programming](https://msdn.microsoft.com/library/hh191443.aspx) pages in the MSDN Library</span></span>
-   <span data-ttu-id="68f87-119">[如何使用 Async 构建 ASP.NET Web 应用程序](http://channel9.msdn.com/events/teched/northamerica/2013/dev-b337)(包括提高服务器吞吐量的演示)</span><span class="sxs-lookup"><span data-stu-id="68f87-119">[How to Build ASP.NET Web Applications Using Async](http://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) (includes a demo of increased server throughput)</span></span>

## <a name="create-the-model"></a><span data-ttu-id="68f87-120">创建模型</span><span class="sxs-lookup"><span data-stu-id="68f87-120">Create the model</span></span>

<span data-ttu-id="68f87-121">我们将使用[Code First 工作流](~/ef6/modeling/code-first/workflows/new-database.md)来创建模型并生成数据库, 但异步功能适用于所有 ef 模型, 包括使用 EF 设计器创建的那些模型。</span><span class="sxs-lookup"><span data-stu-id="68f87-121">We’ll be using the [Code First workflow](~/ef6/modeling/code-first/workflows/new-database.md) to create our model and generate the database, however the asynchronous functionality will work with all EF models including those created with the EF Designer.</span></span>

-   <span data-ttu-id="68f87-122">创建控制台应用程序并将其调用**AsyncDemo**</span><span class="sxs-lookup"><span data-stu-id="68f87-122">Create a Console Application and call it **AsyncDemo**</span></span>
-   <span data-ttu-id="68f87-123">添加 EntityFramework NuGet 包</span><span class="sxs-lookup"><span data-stu-id="68f87-123">Add the EntityFramework NuGet package</span></span>
    -   <span data-ttu-id="68f87-124">在解决方案资源管理器中, 右键单击**AsyncDemo**项目</span><span class="sxs-lookup"><span data-stu-id="68f87-124">In Solution Explorer, right-click on the **AsyncDemo** project</span></span>
    -   <span data-ttu-id="68f87-125">选择 "**管理 NuGet 包 ...** "</span><span class="sxs-lookup"><span data-stu-id="68f87-125">Select **Manage NuGet Packages…**</span></span>
    -   <span data-ttu-id="68f87-126">在 "管理 NuGet 包" 对话框中, 选择 "**联机**" 选项卡, 然后选择 " **EntityFramework** " 包</span><span class="sxs-lookup"><span data-stu-id="68f87-126">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
    -   <span data-ttu-id="68f87-127">单击 "**安装**"</span><span class="sxs-lookup"><span data-stu-id="68f87-127">Click **Install**</span></span>
-   <span data-ttu-id="68f87-128">添加具有以下实现的**Model.cs**类</span><span class="sxs-lookup"><span data-stu-id="68f87-128">Add a **Model.cs** class with the following implementation</span></span>

``` csharp
    using System.Collections.Generic;
    using System.Data.Entity;

    namespace AsyncDemo
    {
        public class BloggingContext : DbContext
        {
            public DbSet<Blog> Blogs { get; set; }
            public DbSet<Post> Posts { get; set; }
        }

        public class Blog
        {
            public int BlogId { get; set; }
            public string Name { get; set; }

            public virtual List<Post> Posts { get; set; }
        }

        public class Post
        {
            public int PostId { get; set; }
            public string Title { get; set; }
            public string Content { get; set; }

            public int BlogId { get; set; }
            public virtual Blog Blog { get; set; }
        }
    }
```

 

## <a name="create-a-synchronous-program"></a><span data-ttu-id="68f87-129">创建同步程序</span><span class="sxs-lookup"><span data-stu-id="68f87-129">Create a synchronous program</span></span>

<span data-ttu-id="68f87-130">现在我们有了 EF 模型, 接下来我们编写一些代码, 用它来执行某些数据访问。</span><span class="sxs-lookup"><span data-stu-id="68f87-130">Now that we have an EF model, let's write some code that uses it to perform some data access.</span></span>

-   <span data-ttu-id="68f87-131">将**Program.cs**的内容替换为以下代码</span><span class="sxs-lookup"><span data-stu-id="68f87-131">Replace the contents of **Program.cs** with the following code</span></span>

``` csharp
    using System;
    using System.Linq;

    namespace AsyncDemo
    {
        class Program
        {
            static void Main(string[] args)
            {
                PerformDatabaseOperations();

                Console.WriteLine("Quote of the day");
                Console.WriteLine(" Don't worry about the world coming to an end today... ");
                Console.WriteLine(" It's already tomorrow in Australia.");

                Console.WriteLine();
                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

            public static void PerformDatabaseOperations()
            {
                using (var db = new BloggingContext())
                {
                    // Create a new blog and save it
                    db.Blogs.Add(new Blog
                    {
                        Name = "Test Blog #" + (db.Blogs.Count() + 1)
                    });
                    Console.WriteLine("Calling SaveChanges.");
                    db.SaveChanges();
                    Console.WriteLine("SaveChanges completed.");

                    // Query for all blogs ordered by name
                    Console.WriteLine("Executing query.");
                    var blogs = (from b in db.Blogs
                                orderby b.Name
                                select b).ToList();

                    // Write all blogs out to Console
                    Console.WriteLine("Query completed with following results:");
                    foreach (var blog in blogs)
                    {
                        Console.WriteLine(" " + blog.Name);
                    }
                }
            }
        }
    }
```

<span data-ttu-id="68f87-132">此代码调用**PerformDatabaseOperations**方法, 该方法将新的**博客**保存到数据库, 然后从数据库中检索所有**博客**并将其打印到**控制台**。</span><span class="sxs-lookup"><span data-stu-id="68f87-132">This code calls the **PerformDatabaseOperations** method which saves a new **Blog** to the database and then retrieves all **Blogs** from the database and prints them to the **Console**.</span></span> <span data-ttu-id="68f87-133">完成此操作后, 程序将一天的报价写入**控制台**。</span><span class="sxs-lookup"><span data-stu-id="68f87-133">After this, the program writes a quote of the day to the **Console**.</span></span>

<span data-ttu-id="68f87-134">由于代码是同步的, 因此, 当我们运行程序时, 可以观察到以下执行流:</span><span class="sxs-lookup"><span data-stu-id="68f87-134">Since the code is synchronous, we can observe the following execution flow when we run the program:</span></span>

1.  <span data-ttu-id="68f87-135">**SaveChanges**开始将新**博客**推送到数据库</span><span class="sxs-lookup"><span data-stu-id="68f87-135">**SaveChanges** begins to push the new **Blog** to the database</span></span>
2.  <span data-ttu-id="68f87-136">**SaveChanges**完成</span><span class="sxs-lookup"><span data-stu-id="68f87-136">**SaveChanges** completes</span></span>
3.  <span data-ttu-id="68f87-137">所有**博客**的查询都发送到数据库</span><span class="sxs-lookup"><span data-stu-id="68f87-137">Query for all **Blogs** is sent to the database</span></span>
4.  <span data-ttu-id="68f87-138">查询返回并将结果写入**控制台**</span><span class="sxs-lookup"><span data-stu-id="68f87-138">Query returns and results are written to **Console**</span></span>
5.  <span data-ttu-id="68f87-139">将日报价写入**控制台**</span><span class="sxs-lookup"><span data-stu-id="68f87-139">Quote of the day is written to **Console**</span></span>

![同步输出](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a><span data-ttu-id="68f87-141">使其异步</span><span class="sxs-lookup"><span data-stu-id="68f87-141">Making it asynchronous</span></span>

<span data-ttu-id="68f87-142">现在, 我们已启动并运行程序, 接下来可以开始使用新的 async 和 await 关键字。</span><span class="sxs-lookup"><span data-stu-id="68f87-142">Now that we have our program up and running, we can begin making use of the new async and await keywords.</span></span> <span data-ttu-id="68f87-143">我们已对 Program.cs 进行了以下更改</span><span class="sxs-lookup"><span data-stu-id="68f87-143">We've made the following changes to Program.cs</span></span>

1.  <span data-ttu-id="68f87-144">第2行:**System.web**命名空间的 using 语句使我们能够访问 EF async extension 方法。</span><span class="sxs-lookup"><span data-stu-id="68f87-144">Line 2: The using statement for the **System.Data.Entity** namespace gives us access to the EF async extension methods.</span></span>
2.  <span data-ttu-id="68f87-145">第4行:用于 " **system.object**命名空间" 命名空间的 using 语句允许我们使用**任务**类型。</span><span class="sxs-lookup"><span data-stu-id="68f87-145">Line 4: The using statement for the **System.Threading.Tasks** namespace allows us to use the **Task** type.</span></span>
3.  <span data-ttu-id="68f87-146">第12行 & 18:我们正在捕获作为任务, 用于监视**PerformSomeDatabaseOperations** (第12行) 的进度, 并在程序的所有工作完成后阻止程序执行完成 (第18行)。</span><span class="sxs-lookup"><span data-stu-id="68f87-146">Line 12 & 18: We are capturing as task that monitors the progress of **PerformSomeDatabaseOperations** (line 12) and then blocking program execution for this task to complete once all the work for the program is done (line 18).</span></span>
4.  <span data-ttu-id="68f87-147">第25行:我们更新了**PerformSomeDatabaseOperations** , 将其标记为**async**并返回一个**任务**。</span><span class="sxs-lookup"><span data-stu-id="68f87-147">Line 25: We've update **PerformSomeDatabaseOperations** to be marked as **async** and return a **Task**.</span></span>
5.  <span data-ttu-id="68f87-148">第35行:我们现在正在调用方法的异步版本, 并等待其完成。</span><span class="sxs-lookup"><span data-stu-id="68f87-148">Line 35: We're now calling the Async version of SaveChanges and awaiting it's completion.</span></span>
6.  <span data-ttu-id="68f87-149">第42行:我们现在正在调用 System.linq.enumerable.tolist 的异步版本, 并等待结果。</span><span class="sxs-lookup"><span data-stu-id="68f87-149">Line 42: We're now calling the Async version of ToList and awaiting on the result.</span></span>

<span data-ttu-id="68f87-150">有关 System.web 命名空间中可用扩展方法的完整列表, 请参阅 QueryableExtensions 类。</span><span class="sxs-lookup"><span data-stu-id="68f87-150">For a comprehensive list of available extension methods in the System.Data.Entity namespace, refer to the QueryableExtensions class.</span></span> <span data-ttu-id="68f87-151">*还需要向 using 语句添加 "使用 System.web"。*</span><span class="sxs-lookup"><span data-stu-id="68f87-151">*You’ll also need to add “using System.Data.Entity” to your using statements.*</span></span>

``` csharp
    using System;
    using System.Data.Entity;
    using System.Linq;
    using System.Threading.Tasks;

    namespace AsyncDemo
    {
        class Program
        {
            static void Main(string[] args)
            {
                var task = PerformDatabaseOperations();

                Console.WriteLine("Quote of the day");
                Console.WriteLine(" Don't worry about the world coming to an end today... ");
                Console.WriteLine(" It's already tomorrow in Australia.");

                task.Wait();

                Console.WriteLine();
                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

            public static async Task PerformDatabaseOperations()
            {
                using (var db = new BloggingContext())
                {
                    // Create a new blog and save it
                    db.Blogs.Add(new Blog
                    {
                        Name = "Test Blog #" + (db.Blogs.Count() + 1)
                    });
                    Console.WriteLine("Calling SaveChanges.");
                    await db.SaveChangesAsync();
                    Console.WriteLine("SaveChanges completed.");

                    // Query for all blogs ordered by name
                    Console.WriteLine("Executing query.");
                    var blogs = await (from b in db.Blogs
                                orderby b.Name
                                select b).ToListAsync();

                    // Write all blogs out to Console
                    Console.WriteLine("Query completed with following results:");
                    foreach (var blog in blogs)
                    {
                        Console.WriteLine(" - " + blog.Name);
                    }
                }
            }
        }
    }
```

<span data-ttu-id="68f87-152">由于代码是异步的, 因此, 我们可以在运行程序时观察到不同的执行流程:</span><span class="sxs-lookup"><span data-stu-id="68f87-152">Now that the code is asynchronous, we can observe a different execution flow when we run the program:</span></span>

1.  <span data-ttu-id="68f87-153">将命令发送到数据库后, **SaveChanges**开始将*新的**博客**推送到数据库。当前托管线程上不再需要计算时间。**PerformDatabaseOperations**方法返回 (即使尚未执行完毕) 和 Main 方法中的程序流将继续。*</span><span class="sxs-lookup"><span data-stu-id="68f87-153">**SaveChanges** begins to push the new **Blog** to the database *Once the command is sent to the database no more compute time is needed on the current managed thread. The **PerformDatabaseOperations** method returns (even though it hasn't finished executing) and program flow in the Main method continues.*</span></span>
2.  <span data-ttu-id="68f87-154">**将日期的 Quote 写入控制台**
     *, 因为在 Main 方法中没有更多的工作要做, 因此, 在数据库操作完成前, 会阻止在等待调用上托管线程。完成后, 我们将执行**PerformDatabaseOperations**的剩余部分。*</span><span class="sxs-lookup"><span data-stu-id="68f87-154">**Quote of the day is written to Console**
*Since there is no more work to do in the Main method, the managed thread is blocked on the Wait call until the database operation completes. Once it completes, the remainder of our **PerformDatabaseOperations** will be executed.*</span></span>
3.  <span data-ttu-id="68f87-155">**SaveChanges**完成</span><span class="sxs-lookup"><span data-stu-id="68f87-155">**SaveChanges** completes</span></span>
4.  <span data-ttu-id="68f87-156">将对所有**博客**的查询再次发送到*数据库, 在数据库中处理查询时, 托管线程可以随意执行其他工作。由于所有其他执行都已完成, 线程只会在等待调用时停止。*</span><span class="sxs-lookup"><span data-stu-id="68f87-156">Query for all **Blogs** is sent to the database *Again, the managed thread is free to do other work while the query is processed in the database. Since all other execution has completed, the thread will just halt on the Wait call though.*</span></span>
5.  <span data-ttu-id="68f87-157">查询返回并将结果写入**控制台**</span><span class="sxs-lookup"><span data-stu-id="68f87-157">Query returns and results are written to **Console**</span></span>

![异步输出](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a><span data-ttu-id="68f87-159">要点在于</span><span class="sxs-lookup"><span data-stu-id="68f87-159">The takeaway</span></span>

<span data-ttu-id="68f87-160">现在, 我们看到了使用 EF 的异步方法是多么简单。</span><span class="sxs-lookup"><span data-stu-id="68f87-160">We now saw how easy it is to make use of EF’s asynchronous methods.</span></span> <span data-ttu-id="68f87-161">尽管 async 的优点在简单的控制台应用程序中可能并不是很明显, 但在运行长时间运行或网络绑定的活动可能会阻止应用程序或导致大量线程增加内存占用量。</span><span class="sxs-lookup"><span data-stu-id="68f87-161">Although the advantages of async may not be very apparent with a simple console app, these same strategies can be applied in situations where long-running or network-bound activities might otherwise block the application, or cause a large number of threads to increase the memory footprint.</span></span>
