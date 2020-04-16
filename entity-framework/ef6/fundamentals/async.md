---
title: 异步查询和保存 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: d56e6f1d-4bd1-4b50-9558-9a30e04a8ec3
ms.openlocfilehash: a001df726b7cd3342fcca566c1373f452811d07d
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434235"
---
# <a name="async-query-and-save"></a><span data-ttu-id="42bc5-102">异步查询和保存</span><span class="sxs-lookup"><span data-stu-id="42bc5-102">Async query and save</span></span>
> [!NOTE]
> <span data-ttu-id="42bc5-103">**仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。</span><span class="sxs-lookup"><span data-stu-id="42bc5-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="42bc5-104">如果使用的是早期版本，则部分或全部信息不适用。</span><span class="sxs-lookup"><span data-stu-id="42bc5-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="42bc5-105">EF6 引入了对异步查询的支持，并使用 .NET 4.5 中引入[的异步和等待关键字](https://msdn.microsoft.com/library/vstudio/hh191443.aspx)进行保存。</span><span class="sxs-lookup"><span data-stu-id="42bc5-105">EF6 introduced support for asynchronous query and save using the [async and await keywords](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) that were introduced in .NET 4.5.</span></span> <span data-ttu-id="42bc5-106">虽然并非所有应用程序都可能从异步中受益，但它可用于在处理长时间运行、网络或 I/O 绑定任务时提高客户端响应能力和服务器可扩展性。</span><span class="sxs-lookup"><span data-stu-id="42bc5-106">While not all applications may benefit from asynchrony, it can be used to improve client responsiveness and server scalability when handling long-running, network or I/O-bound tasks.</span></span>

## <a name="when-to-really-use-async"></a><span data-ttu-id="42bc5-107">何时真正使用异步</span><span class="sxs-lookup"><span data-stu-id="42bc5-107">When to really use async</span></span>

<span data-ttu-id="42bc5-108">本演练的目的是以一种易于观察异步和同步程序执行之间的区别的方式介绍异步概念。</span><span class="sxs-lookup"><span data-stu-id="42bc5-108">The purpose of this walkthrough is to introduce the async concepts in a way that makes it easy to observe the difference between asynchronous and synchronous program execution.</span></span> <span data-ttu-id="42bc5-109">本演练的目的不是说明异步编程带来好处的任何关键方案。</span><span class="sxs-lookup"><span data-stu-id="42bc5-109">This walkthrough is not intended to illustrate any of the key scenarios where async programming provides benefits.</span></span>

<span data-ttu-id="42bc5-110">异步编程主要侧重于释放当前托管线程（线程运行 .NET 代码），以执行其他工作，同时等待不需要从托管线程进行任何计算时间的操作。</span><span class="sxs-lookup"><span data-stu-id="42bc5-110">Async programming is primarily focused on freeing up the current managed thread (thread running .NET code) to do other work while it waits for an operation that does not require any compute time from a managed thread.</span></span> <span data-ttu-id="42bc5-111">例如，当数据库引擎正在处理查询时，.NET 代码将执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="42bc5-111">For example, whilst the database engine is processing a query there is nothing to be done by .NET code.</span></span>

<span data-ttu-id="42bc5-112">在客户端应用程序（WinForms、WPF 等）中，当前线程可用于执行异步操作时保持 UI 响应。</span><span class="sxs-lookup"><span data-stu-id="42bc5-112">In client applications (WinForms, WPF, etc.) the current thread can be used to keep the UI responsive while the async operation is performed.</span></span> <span data-ttu-id="42bc5-113">在服务器应用程序（ASP.NET等）中，线程可用于处理其他传入请求 - 这可以减少内存使用量和/或提高服务器的吞吐量。</span><span class="sxs-lookup"><span data-stu-id="42bc5-113">In server applications (ASP.NET etc.) the thread can be used to process other incoming requests - this can reduce memory usage and/or increase throughput of the server.</span></span>

<span data-ttu-id="42bc5-114">在大多数使用异步的应用程序将没有明显的好处，甚至可能是有害的。</span><span class="sxs-lookup"><span data-stu-id="42bc5-114">In most applications using async will have no noticeable benefits and even could be detrimental.</span></span> <span data-ttu-id="42bc5-115">在提交特定方案之前，使用测试、分析和常识来测量异步对特定方案的影响。</span><span class="sxs-lookup"><span data-stu-id="42bc5-115">Use tests, profiling and common sense to measure the impact of async in your particular scenario before committing to it.</span></span>

<span data-ttu-id="42bc5-116">以下是一些更多有关异步的资源：</span><span class="sxs-lookup"><span data-stu-id="42bc5-116">Here are some more resources to learn about async:</span></span>

-   [<span data-ttu-id="42bc5-117">布兰登·布雷在 .NET 4.5 中的异步/等待概述</span><span class="sxs-lookup"><span data-stu-id="42bc5-117">Brandon Bray’s overview of async/await in .NET 4.5</span></span>](https://devblogs.microsoft.com/dotnet/async-in-4-5-worth-the-await/)
-   <span data-ttu-id="42bc5-118">MSDN 库中的[异步编程](https://msdn.microsoft.com/library/hh191443.aspx)页面</span><span class="sxs-lookup"><span data-stu-id="42bc5-118">[Asynchronous Programming](https://msdn.microsoft.com/library/hh191443.aspx) pages in the MSDN Library</span></span>
-   <span data-ttu-id="42bc5-119">[如何使用 Async 构建ASP.NET Web 应用程序](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337)（包括服务器吞吐量增加的演示）</span><span class="sxs-lookup"><span data-stu-id="42bc5-119">[How to Build ASP.NET Web Applications Using Async](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) (includes a demo of increased server throughput)</span></span>

## <a name="create-the-model"></a><span data-ttu-id="42bc5-120">创建模型</span><span class="sxs-lookup"><span data-stu-id="42bc5-120">Create the model</span></span>

<span data-ttu-id="42bc5-121">我们将使用[Code First 工作流](~/ef6/modeling/code-first/workflows/new-database.md)创建模型并生成数据库，但是异步功能将适用于所有 EF 模型，包括使用 EF 设计器创建的模型。</span><span class="sxs-lookup"><span data-stu-id="42bc5-121">We’ll be using the [Code First workflow](~/ef6/modeling/code-first/workflows/new-database.md) to create our model and generate the database, however the asynchronous functionality will work with all EF models including those created with the EF Designer.</span></span>

-   <span data-ttu-id="42bc5-122">创建控制台应用程序并称之为**AsyncDemo**</span><span class="sxs-lookup"><span data-stu-id="42bc5-122">Create a Console Application and call it **AsyncDemo**</span></span>
-   <span data-ttu-id="42bc5-123">添加实体框架 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="42bc5-123">Add the EntityFramework NuGet package</span></span>
    -   <span data-ttu-id="42bc5-124">在解决方案资源管理器中，右键单击**AsyncDemo**项目</span><span class="sxs-lookup"><span data-stu-id="42bc5-124">In Solution Explorer, right-click on the **AsyncDemo** project</span></span>
    -   <span data-ttu-id="42bc5-125">选择 **"管理 NuGet 包..."...**</span><span class="sxs-lookup"><span data-stu-id="42bc5-125">Select **Manage NuGet Packages…**</span></span>
    -   <span data-ttu-id="42bc5-126">在"管理 NuGet 包"对话框中，选择 **"联机**"选项卡并选择 **"实体框架**"包</span><span class="sxs-lookup"><span data-stu-id="42bc5-126">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
    -   <span data-ttu-id="42bc5-127">单击"**安装"**</span><span class="sxs-lookup"><span data-stu-id="42bc5-127">Click **Install**</span></span>
-   <span data-ttu-id="42bc5-128">添加具有以下实现**Model.cs**类</span><span class="sxs-lookup"><span data-stu-id="42bc5-128">Add a **Model.cs** class with the following implementation</span></span>

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

 

## <a name="create-a-synchronous-program"></a><span data-ttu-id="42bc5-129">创建同步程序</span><span class="sxs-lookup"><span data-stu-id="42bc5-129">Create a synchronous program</span></span>

<span data-ttu-id="42bc5-130">现在，我们有一个 EF 模型，让我们编写一些代码，用它来执行一些数据访问。</span><span class="sxs-lookup"><span data-stu-id="42bc5-130">Now that we have an EF model, let's write some code that uses it to perform some data access.</span></span>

-   <span data-ttu-id="42bc5-131">将**Program.cs**的内容替换为以下代码</span><span class="sxs-lookup"><span data-stu-id="42bc5-131">Replace the contents of **Program.cs** with the following code</span></span>

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

<span data-ttu-id="42bc5-132">此代码调用**执行数据库操作**方法，该方法将新**博客**保存到数据库中，然后从数据库中检索所有**博客**并将其打印到**控制台**。</span><span class="sxs-lookup"><span data-stu-id="42bc5-132">This code calls the **PerformDatabaseOperations** method which saves a new **Blog** to the database and then retrieves all **Blogs** from the database and prints them to the **Console**.</span></span> <span data-ttu-id="42bc5-133">在此之后，程序将当天的报价写入**控制台**。</span><span class="sxs-lookup"><span data-stu-id="42bc5-133">After this, the program writes a quote of the day to the **Console**.</span></span>

<span data-ttu-id="42bc5-134">由于代码是同步的，因此在运行程序时，我们可以观察到以下执行流：</span><span class="sxs-lookup"><span data-stu-id="42bc5-134">Since the code is synchronous, we can observe the following execution flow when we run the program:</span></span>

1.  <span data-ttu-id="42bc5-135">**保存更改**开始将新**博客**推送到数据库</span><span class="sxs-lookup"><span data-stu-id="42bc5-135">**SaveChanges** begins to push the new **Blog** to the database</span></span>
2.  <span data-ttu-id="42bc5-136">**保存更改**完成</span><span class="sxs-lookup"><span data-stu-id="42bc5-136">**SaveChanges** completes</span></span>
3.  <span data-ttu-id="42bc5-137">查询所有**博客**将发送到数据库</span><span class="sxs-lookup"><span data-stu-id="42bc5-137">Query for all **Blogs** is sent to the database</span></span>
4.  <span data-ttu-id="42bc5-138">查询返回和结果写入**控制台**</span><span class="sxs-lookup"><span data-stu-id="42bc5-138">Query returns and results are written to **Console**</span></span>
5.  <span data-ttu-id="42bc5-139">当天的报价写入**控制台**</span><span class="sxs-lookup"><span data-stu-id="42bc5-139">Quote of the day is written to **Console**</span></span>

![同步输出](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a><span data-ttu-id="42bc5-141">使其异步</span><span class="sxs-lookup"><span data-stu-id="42bc5-141">Making it asynchronous</span></span>

<span data-ttu-id="42bc5-142">现在，我们已经启动和运行我们的程序，我们可以开始使用新的异步和等待关键字。</span><span class="sxs-lookup"><span data-stu-id="42bc5-142">Now that we have our program up and running, we can begin making use of the new async and await keywords.</span></span> <span data-ttu-id="42bc5-143">我们对Program.cs进行了以下更改</span><span class="sxs-lookup"><span data-stu-id="42bc5-143">We've made the following changes to Program.cs</span></span>

1.  <span data-ttu-id="42bc5-144">第 2 行 **：System.Data.Entity**命名空间的 using 语句允许我们访问 EF 异步扩展方法。</span><span class="sxs-lookup"><span data-stu-id="42bc5-144">Line 2: The using statement for the **System.Data.Entity** namespace gives us access to the EF async extension methods.</span></span>
2.  <span data-ttu-id="42bc5-145">第 4 行：**系统.线程的**using 语句，任务命名空间允许我们使用**任务**类型。</span><span class="sxs-lookup"><span data-stu-id="42bc5-145">Line 4: The using statement for the **System.Threading.Tasks** namespace allows us to use the **Task** type.</span></span>
3.  <span data-ttu-id="42bc5-146">第 12 行 & 18：我们将捕获为任务，用于监视**执行某些数据库操作**（第 12 行）的进度，然后在完成程序的所有工作（第 18 行）后阻止此任务的程序执行完成。</span><span class="sxs-lookup"><span data-stu-id="42bc5-146">Line 12 & 18: We are capturing as task that monitors the progress of **PerformSomeDatabaseOperations** (line 12) and then blocking program execution for this task to complete once all the work for the program is done (line 18).</span></span>
4.  <span data-ttu-id="42bc5-147">第 25 行：我们更新了**执行一些数据库操作**，以标记为**异步**并返回**任务**。</span><span class="sxs-lookup"><span data-stu-id="42bc5-147">Line 25: We've update **PerformSomeDatabaseOperations** to be marked as **async** and return a **Task**.</span></span>
5.  <span data-ttu-id="42bc5-148">第 35 行：我们现在正在调用"保存更改"的 Async 版本，等待它完成。</span><span class="sxs-lookup"><span data-stu-id="42bc5-148">Line 35: We're now calling the Async version of SaveChanges and awaiting it's completion.</span></span>
6.  <span data-ttu-id="42bc5-149">第 42 行：我们现在正在调用 ToList 的 Async 版本，等待结果。</span><span class="sxs-lookup"><span data-stu-id="42bc5-149">Line 42: We're now calling the Async version of ToList and awaiting on the result.</span></span>

<span data-ttu-id="42bc5-150">有关 System.Data.Entity 命名空间中可用扩展方法的完整列表，请参阅可查询扩展类。</span><span class="sxs-lookup"><span data-stu-id="42bc5-150">For a comprehensive list of available extension methods in the System.Data.Entity namespace, refer to the QueryableExtensions class.</span></span> <span data-ttu-id="42bc5-151">*您还需要将"使用系统.Data.Entity"添加到您的使用语句中。*</span><span class="sxs-lookup"><span data-stu-id="42bc5-151">*You’ll also need to add “using System.Data.Entity” to your using statements.*</span></span>

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

<span data-ttu-id="42bc5-152">现在代码是异步的，我们可以在运行程序时观察到不同的执行流：</span><span class="sxs-lookup"><span data-stu-id="42bc5-152">Now that the code is asynchronous, we can observe a different execution flow when we run the program:</span></span>

1. <span data-ttu-id="42bc5-153">**保存更改**开始将新**博客**推送到数据库</span><span class="sxs-lookup"><span data-stu-id="42bc5-153">**SaveChanges** begins to push the new **Blog** to the database</span></span>  
    <span data-ttu-id="42bc5-154">*将命令发送到数据库后，当前托管线程不需要更多的计算时间。**执行数据库操作**方法返回（即使它尚未完成执行），并且 Main 方法中的程序流将继续。*</span><span class="sxs-lookup"><span data-stu-id="42bc5-154">*Once the command is sent to the database no more compute time is needed on the current managed thread. The **PerformDatabaseOperations** method returns (even though it hasn't finished executing) and program flow in the Main method continues.*</span></span>
2. <span data-ttu-id="42bc5-155">**当天的报价写入控制台**</span><span class="sxs-lookup"><span data-stu-id="42bc5-155">**Quote of the day is written to Console**</span></span>  
    <span data-ttu-id="42bc5-156">*由于 Main 方法中没有更多工作要做，因此在 Wait 调用上阻止托管线程，直到数据库操作完成。完成后，将执行**其余执行数据库操作**。*</span><span class="sxs-lookup"><span data-stu-id="42bc5-156">*Since there is no more work to do in the Main method, the managed thread is blocked on the Wait call until the database operation completes. Once it completes, the remainder of our **PerformDatabaseOperations** will be executed.*</span></span>
3.  <span data-ttu-id="42bc5-157">**保存更改**完成</span><span class="sxs-lookup"><span data-stu-id="42bc5-157">**SaveChanges** completes</span></span>  
4.  <span data-ttu-id="42bc5-158">查询所有**博客**将发送到数据库</span><span class="sxs-lookup"><span data-stu-id="42bc5-158">Query for all **Blogs** is sent to the database</span></span>  
    <span data-ttu-id="42bc5-159">*同样，托管线程在数据库中处理查询时可以自由地执行其他工作。由于所有其他执行已完成，线程将在"等待"调用时停止。*</span><span class="sxs-lookup"><span data-stu-id="42bc5-159">*Again, the managed thread is free to do other work while the query is processed in the database. Since all other execution has completed, the thread will just halt on the Wait call though.*</span></span>
5.  <span data-ttu-id="42bc5-160">查询返回和结果写入**控制台**</span><span class="sxs-lookup"><span data-stu-id="42bc5-160">Query returns and results are written to **Console**</span></span>  

![异步输出](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a><span data-ttu-id="42bc5-162">外卖</span><span class="sxs-lookup"><span data-stu-id="42bc5-162">The takeaway</span></span>

<span data-ttu-id="42bc5-163">现在，我们看到使用 EF 的异步方法是多么容易。</span><span class="sxs-lookup"><span data-stu-id="42bc5-163">We now saw how easy it is to make use of EF’s asynchronous methods.</span></span> <span data-ttu-id="42bc5-164">尽管异步的优点在简单的控制台应用中可能并不十分明显，但在长时间运行或网络绑定活动可能会阻塞应用程序或导致大量线程增加内存占用的情况下，可以应用这些相同的策略。</span><span class="sxs-lookup"><span data-stu-id="42bc5-164">Although the advantages of async may not be very apparent with a simple console app, these same strategies can be applied in situations where long-running or network-bound activities might otherwise block the application, or cause a large number of threads to increase the memory footprint.</span></span>
