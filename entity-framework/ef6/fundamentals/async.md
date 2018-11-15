---
title: 异步查询和保存的 EF6
author: divega
ms.date: 10/23/2016
ms.assetid: d56e6f1d-4bd1-4b50-9558-9a30e04a8ec3
ms.openlocfilehash: 4ed4f5c13341f33ccff8325a5ddacd8f7b195a76
ms.sourcegitcommit: 269c8a1a457a9ad27b4026c22c4b1a76991fb360
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2018
ms.locfileid: "46283818"
---
# <a name="async-query-and-save"></a><span data-ttu-id="1d8f0-102">异步查询和保存</span><span class="sxs-lookup"><span data-stu-id="1d8f0-102">Async query and save</span></span>
> [!NOTE]
> <span data-ttu-id="1d8f0-103">**仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。</span><span class="sxs-lookup"><span data-stu-id="1d8f0-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="1d8f0-104">如果使用的是早期版本，则部分或全部信息不适用。</span><span class="sxs-lookup"><span data-stu-id="1d8f0-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="1d8f0-105">EF6 引入了异步查询和保存使用的支持[async 和 await 关键字](https://msdn.microsoft.com/library/vstudio/hh191443.aspx).NET 4.5 中引入的。</span><span class="sxs-lookup"><span data-stu-id="1d8f0-105">EF6 introduced support for asynchronous query and save using the [async and await keywords](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) that were introduced in .NET 4.5.</span></span> <span data-ttu-id="1d8f0-106">虽然并非所有应用程序可能会受益于异步功能，它可以用于处理长时间运行、 网络或 O 绑定任务时提高客户端响应能力和服务器可伸缩性。</span><span class="sxs-lookup"><span data-stu-id="1d8f0-106">While not all applications may benefit from asynchrony, it can be used to improve client responsiveness and server scalability when handling long-running, network or I/O-bound tasks.</span></span>

## <a name="when-to-really-use-async"></a><span data-ttu-id="1d8f0-107">何时真正使用异步</span><span class="sxs-lookup"><span data-stu-id="1d8f0-107">When to really use async</span></span>

<span data-ttu-id="1d8f0-108">本演练的目的是介绍了异步概念的方式，轻松地观察异步和同步计划执行之间的差异。</span><span class="sxs-lookup"><span data-stu-id="1d8f0-108">The purpose of this walkthrough is to introduce the async concepts in a way that makes it easy to observe the difference between asynchronous and synchronous program execution.</span></span> <span data-ttu-id="1d8f0-109">本演练不是为了说明的重要方案的任何异步编程优势。</span><span class="sxs-lookup"><span data-stu-id="1d8f0-109">This walkthrough is not intended to illustrate any of the key scenarios where async programming provides benefits.</span></span>

<span data-ttu-id="1d8f0-110">异步编程是主要侧重于释放当前托管线程 （线程正在运行的.NET 代码） 来执行其他工作，等待不需要任何计算时间的操作时从托管线程。</span><span class="sxs-lookup"><span data-stu-id="1d8f0-110">Async programming is primarily focused on freeing up the current managed thread (thread running .NET code) to do other work while it waits for an operation that does not require any compute time from a managed thread.</span></span> <span data-ttu-id="1d8f0-111">例如，尽管数据库引擎处理查询时没有要通过.NET 代码。</span><span class="sxs-lookup"><span data-stu-id="1d8f0-111">For example, whilst the database engine is processing a query there is nothing to be done by .NET code.</span></span>

<span data-ttu-id="1d8f0-112">在客户端应用程序 （WinForms、 WPF 等） 中可以使用当前线程来执行异步操作时保持 UI 响应能力。</span><span class="sxs-lookup"><span data-stu-id="1d8f0-112">In client applications (WinForms, WPF, etc.) the current thread can be used to keep the UI responsive while the async operation is performed.</span></span> <span data-ttu-id="1d8f0-113">在服务器应用程序 （ASP.NET 等） 可以使用线程以处理其他传入的请求-这可以减少内存使用量和/或增加服务器的吞吐量。</span><span class="sxs-lookup"><span data-stu-id="1d8f0-113">In server applications (ASP.NET etc.) the thread can be used to process other incoming requests - this can reduce memory usage and/or increase throughput of the server.</span></span>

<span data-ttu-id="1d8f0-114">在大多数应用程序使用 async 会有任何明显的好处，甚至可能是造成不利影响。</span><span class="sxs-lookup"><span data-stu-id="1d8f0-114">In most applications using async will have no noticeable benefits and even could be detrimental.</span></span> <span data-ttu-id="1d8f0-115">使用测试、 分析和常识来提交前测量在特定方案的 async 的影响。</span><span class="sxs-lookup"><span data-stu-id="1d8f0-115">Use tests, profiling and common sense to measure the impact of async in your particular scenario before committing to it.</span></span>

<span data-ttu-id="1d8f0-116">下面是一些更多资源，以了解异步：</span><span class="sxs-lookup"><span data-stu-id="1d8f0-116">Here are some more resources to learn about async:</span></span>

-   [<span data-ttu-id="1d8f0-117">Brandon Bray 的 async/await.NET 4.5 中的概述</span><span class="sxs-lookup"><span data-stu-id="1d8f0-117">Brandon Bray’s overview of async/await in .NET 4.5</span></span>](https://blogs.msdn.com/b/dotnet/archive/2012/04/03/async-in-4-5-worth-the-await.aspx)
-   <span data-ttu-id="1d8f0-118">[异步编程](https://msdn.microsoft.com/library/hh191443.aspx)MSDN Library 中的页</span><span class="sxs-lookup"><span data-stu-id="1d8f0-118">[Asynchronous Programming](https://msdn.microsoft.com/library/hh191443.aspx) pages in the MSDN Library</span></span>
-   <span data-ttu-id="1d8f0-119">[如何生成 ASP.NET Web 应用程序使用 Async](http://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) （包括提高的服务器的吞吐量的演示）</span><span class="sxs-lookup"><span data-stu-id="1d8f0-119">[How to Build ASP.NET Web Applications Using Async](http://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) (includes a demo of increased server throughput)</span></span>

## <a name="create-the-model"></a><span data-ttu-id="1d8f0-120">创建模型</span><span class="sxs-lookup"><span data-stu-id="1d8f0-120">Create the model</span></span>

<span data-ttu-id="1d8f0-121">我们将使用[Code First 工作流](~/ef6/modeling/code-first/workflows/new-database.md)来创建我们的模型，但异步功能将使用包括那些使用 EF 设计器创建的所有 EF 模型生成数据库。</span><span class="sxs-lookup"><span data-stu-id="1d8f0-121">We’ll be using the [Code First workflow](~/ef6/modeling/code-first/workflows/new-database.md) to create our model and generate the database, however the asynchronous functionality will work with all EF models including those created with the EF Designer.</span></span>

-   <span data-ttu-id="1d8f0-122">创建一个控制台应用程序并对其**AsyncDemo**</span><span class="sxs-lookup"><span data-stu-id="1d8f0-122">Create a Console Application and call it **AsyncDemo**</span></span>
-   <span data-ttu-id="1d8f0-123">添加 EntityFramework NuGet 包</span><span class="sxs-lookup"><span data-stu-id="1d8f0-123">Add the EntityFramework NuGet package</span></span>
    -   <span data-ttu-id="1d8f0-124">在解决方案资源管理器，右键单击**AsyncDemo**项目</span><span class="sxs-lookup"><span data-stu-id="1d8f0-124">In Solution Explorer, right-click on the **AsyncDemo** project</span></span>
    -   <span data-ttu-id="1d8f0-125">选择**管理 NuGet 包...**</span><span class="sxs-lookup"><span data-stu-id="1d8f0-125">Select **Manage NuGet Packages…**</span></span>
    -   <span data-ttu-id="1d8f0-126">在管理 NuGet 包对话框中，选择**联机**选项卡，选择**EntityFramework**包</span><span class="sxs-lookup"><span data-stu-id="1d8f0-126">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
    -   <span data-ttu-id="1d8f0-127">单击**安装**</span><span class="sxs-lookup"><span data-stu-id="1d8f0-127">Click **Install**</span></span>
-   <span data-ttu-id="1d8f0-128">添加**Model.cs**类具有以下实现</span><span class="sxs-lookup"><span data-stu-id="1d8f0-128">Add a **Model.cs** class with the following implementation</span></span>

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

 

## <a name="create-a-synchronous-program"></a><span data-ttu-id="1d8f0-129">创建一个同步程序，</span><span class="sxs-lookup"><span data-stu-id="1d8f0-129">Create a synchronous program</span></span>

<span data-ttu-id="1d8f0-130">现在，我们已有 EF 模型，让我们编写一些代码来使用它来执行某些数据访问。</span><span class="sxs-lookup"><span data-stu-id="1d8f0-130">Now that we have an EF model, let's write some code that uses it to perform some data access.</span></span>

-   <span data-ttu-id="1d8f0-131">内容替换为**Program.cs**用下面的代码</span><span class="sxs-lookup"><span data-stu-id="1d8f0-131">Replace the contents of **Program.cs** with the following code</span></span>

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

                Console.WriteLine();
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
                    db.SaveChanges();

                    // Query for all blogs ordered by name
                    var blogs = (from b in db.Blogs
                                orderby b.Name
                                select b).ToList();

                    // Write all blogs out to Console
                    Console.WriteLine();
                    Console.WriteLine("All blogs:");
                    foreach (var blog in blogs)
                    {
                        Console.WriteLine(" " + blog.Name);
                    }
                }
            }
        }
    }
```

<span data-ttu-id="1d8f0-132">此代码将调用**PerformDatabaseOperations**方法，从而节省了一个新**博客**到数据库，然后检索所有**博客**从数据库，并输出到**控制台**。</span><span class="sxs-lookup"><span data-stu-id="1d8f0-132">This code calls the **PerformDatabaseOperations** method which saves a new **Blog** to the database and then retrieves all **Blogs** from the database and prints them to the **Console**.</span></span> <span data-ttu-id="1d8f0-133">在此之后，程序写入到一天中的引号**控制台**。</span><span class="sxs-lookup"><span data-stu-id="1d8f0-133">After this, the program writes a quote of the day to the **Console**.</span></span>

<span data-ttu-id="1d8f0-134">因为代码是同步的我们可以观察到以下的执行流，当我们运行程序：</span><span class="sxs-lookup"><span data-stu-id="1d8f0-134">Since the code is synchronous, we can observe the following execution flow when we run the program:</span></span>

1.  <span data-ttu-id="1d8f0-135">**SaveChanges**开始推送新**博客**到数据库</span><span class="sxs-lookup"><span data-stu-id="1d8f0-135">**SaveChanges** begins to push the new **Blog** to the database</span></span>
2.  <span data-ttu-id="1d8f0-136">**SaveChanges**完成</span><span class="sxs-lookup"><span data-stu-id="1d8f0-136">**SaveChanges** completes</span></span>
3.  <span data-ttu-id="1d8f0-137">所有查询**博客**发送到数据库</span><span class="sxs-lookup"><span data-stu-id="1d8f0-137">Query for all **Blogs** is sent to the database</span></span>
4.  <span data-ttu-id="1d8f0-138">查询返回，而结果将写入到**控制台**</span><span class="sxs-lookup"><span data-stu-id="1d8f0-138">Query returns and results are written to **Console**</span></span>
5.  <span data-ttu-id="1d8f0-139">一天中的引号写入到**控制台**</span><span class="sxs-lookup"><span data-stu-id="1d8f0-139">Quote of the day is written to **Console**</span></span>

![同步输出](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a><span data-ttu-id="1d8f0-141">使其异步</span><span class="sxs-lookup"><span data-stu-id="1d8f0-141">Making it asynchronous</span></span>

<span data-ttu-id="1d8f0-142">现在，我们将我们的程序启动并运行，我们可以开始使用新的 async 和 await 关键字。</span><span class="sxs-lookup"><span data-stu-id="1d8f0-142">Now that we have our program up and running, we can begin making use of the new async and await keywords.</span></span> <span data-ttu-id="1d8f0-143">我们已对 Program.cs 所做以下更改</span><span class="sxs-lookup"><span data-stu-id="1d8f0-143">We've made the following changes to Program.cs</span></span>

1.  <span data-ttu-id="1d8f0-144">第 2 行： using 语句**System.Data.Entity**命名空间提供了我们访问到 EF 异步扩展方法。</span><span class="sxs-lookup"><span data-stu-id="1d8f0-144">Line 2: The using statement for the **System.Data.Entity** namespace gives us access to the EF async extension methods.</span></span>
2.  <span data-ttu-id="1d8f0-145">第 4 行： using 语句**System.Threading.Tasks**命名空间，我们便使用**任务**类型。</span><span class="sxs-lookup"><span data-stu-id="1d8f0-145">Line 4: The using statement for the **System.Threading.Tasks** namespace allows us to use the **Task** type.</span></span>
3.  <span data-ttu-id="1d8f0-146">第 12 和 18 行： 我们捕获作为任务，用于监视的进度**PerformSomeDatabaseOperations** （第 12 行），然后阻止此程序执行任务为完成一次的所有工作程序完成 （第 18 行）。</span><span class="sxs-lookup"><span data-stu-id="1d8f0-146">Line 12 & 18: We are capturing as task that monitors the progress of **PerformSomeDatabaseOperations** (line 12) and then blocking program execution for this task to complete once all the work for the program is done (line 18).</span></span>
4.  <span data-ttu-id="1d8f0-147">第 25 行： 我们已更新**PerformSomeDatabaseOperations**要标记为已**异步**并返回**任务**。</span><span class="sxs-lookup"><span data-stu-id="1d8f0-147">Line 25: We've update **PerformSomeDatabaseOperations** to be marked as **async** and return a **Task**.</span></span>
5.  <span data-ttu-id="1d8f0-148">行 35： 我们现在调用 SaveChanges 的异步版本和等待其完成。</span><span class="sxs-lookup"><span data-stu-id="1d8f0-148">Line 35: We're now calling the Async version of SaveChanges and awaiting it's completion.</span></span>
6.  <span data-ttu-id="1d8f0-149">行 42： 我们现在调用 ToList 的异步版本和正等待的结果。</span><span class="sxs-lookup"><span data-stu-id="1d8f0-149">Line 42: We're now calling hte Async version of ToList and awaiting on the result.</span></span>

<span data-ttu-id="1d8f0-150">有关 system.data.entity 的引用命名空间中可用的扩展方法的完整列表，请参阅 QueryableExtensions 类。</span><span class="sxs-lookup"><span data-stu-id="1d8f0-150">For a comprehensive list of available extension methods in the System.Data.Entity namespace, refer to the QueryableExtensions class.</span></span> <span data-ttu-id="1d8f0-151">*您还需要添加"using System.Data.Entity"到语句。*</span><span class="sxs-lookup"><span data-stu-id="1d8f0-151">*You’ll also need to add “using System.Data.Entity” to your using statements.*</span></span>

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

<span data-ttu-id="1d8f0-152">现在，代码为异步，我们可以观察到不同的执行流，当我们运行程序：</span><span class="sxs-lookup"><span data-stu-id="1d8f0-152">Now that the code is asyncronous, we can observe a different execution flow when we run the program:</span></span>

1.  <span data-ttu-id="1d8f0-153">**SaveChanges**开始推送新**博客**到数据库*后将命令发送到该数据库没有更多计算当前托管线程上所需时间。**PerformDatabaseOperations**方法将返回 （即使它尚未完成执行），在 Main 方法中的程序流将继续。*</span><span class="sxs-lookup"><span data-stu-id="1d8f0-153">**SaveChanges** begins to push the new **Blog** to the database *Once the command is sent to the database no more compute time is needed on the current managed thread. The **PerformDatabaseOperations** method returns (even though it hasn't finished executing) and program flow in the Main method continues.*</span></span>
2.  <span data-ttu-id="1d8f0-154">**一天中的引号写入到控制台**
    *由于没有更多工作要做 Main 方法中，托管的线程被阻止在等待上调用，直到数据库操作完成。完成后，其余我们\*\*PerformDatabaseOperations*\* \*将执行。</span><span class="sxs-lookup"><span data-stu-id="1d8f0-154">**Quote of the day is written to Console**
*Since there is no more work to do in the Main method, the managed thread is blocked on the Wait call until the database operation completes. Once it completes, the remainder of our **PerformDatabaseOperations*** will be executed.</span></span>
3.  <span data-ttu-id="1d8f0-155">**SaveChanges**完成</span><span class="sxs-lookup"><span data-stu-id="1d8f0-155">**SaveChanges** completes</span></span>
4.  <span data-ttu-id="1d8f0-156">所有查询**博客**发送到数据库*同样，托管的线程是免费的数据库中处理查询时执行其他工作。由于所有其他执行完成后，该线程将只需暂停等待调用不过。*</span><span class="sxs-lookup"><span data-stu-id="1d8f0-156">Query for all **Blogs** is sent to the database *Again, the managed thread is free to do other work while the query is processed in the database. Since all other execution has completed, the thread will just halt on the Wait call though.*</span></span>
5.  <span data-ttu-id="1d8f0-157">查询返回，而结果将写入到**控制台**</span><span class="sxs-lookup"><span data-stu-id="1d8f0-157">Query returns and results are written to **Console**</span></span>

![异步输出](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a><span data-ttu-id="1d8f0-159">要点</span><span class="sxs-lookup"><span data-stu-id="1d8f0-159">The takeaway</span></span>

<span data-ttu-id="1d8f0-160">我们现在已了解以使是多么的 EF 的异步方法使用。</span><span class="sxs-lookup"><span data-stu-id="1d8f0-160">We now saw how easy it is to make use of EF’s asynchronous methods.</span></span> <span data-ttu-id="1d8f0-161">尽管异步的优点可能不能使用简单的控制台应用程序非常明显，但可以在其中长时间运行或网络密集型活动可能会否则为阻止应用程序，或者导致大量的线程的情况下应用这些相同的策略增加内存占用量。</span><span class="sxs-lookup"><span data-stu-id="1d8f0-161">Although the advantages of async may not be very apparent with a simple console app, these same strategies can be applied in situations where long-running or network-bound activities might otherwise block the application, or cause a large number of threads to increase the memory footprint.</span></span>
