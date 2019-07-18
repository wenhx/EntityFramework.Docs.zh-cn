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
# <a name="async-query-and-save"></a>异步查询并保存
> [!NOTE]
> **仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。 如果使用的是早期版本，则部分或全部信息不适用。

EF6 引入了对异步查询的支持, 并使用 .NET 4.5 中引入的[async 和 await 关键字](https://msdn.microsoft.com/library/vstudio/hh191443.aspx)进行保存。 虽然并非所有应用程序都可以受益于异步, 但它可用于在处理长时间运行的、网络或 i/o 限制的任务时提高客户端的响应能力和服务器的可伸缩性。

## <a name="when-to-really-use-async"></a>何时真正使用 async

本演练的目的是以一种可以轻松地观察异步和同步程序执行之间的差异的方式引入异步概念。 本演练并不旨在说明异步编程提供了好处的关键方案。

异步编程主要侧重于释放当前托管线程 (运行 .NET 代码的线程) 来执行其他工作, 同时等待不需要托管线程的任何计算时间的操作。 例如, 当数据库引擎正在处理查询时, .NET 代码不会执行任何操作。

在客户端应用程序 (WinForms、WPF 等) 中, 当前线程可用于在执行异步操作时保持 UI 的响应能力。 在服务器应用程序 (ASP.NET 等) 中, 线程可用于处理其他传入请求-这可以减少内存使用量和/或提高服务器的吞吐量。

在大多数使用 async 的应用程序中, 没有明显的好处, 甚至可能会造成不利影响。 在提交到特定方案之前, 请使用测试、分析和常见意义来度量异步的影响。

下面是一些用于了解 async 的更多资源:

-   [.NET 4.5 中的 Brandon Bray 概述](https://blogs.msdn.com/b/dotnet/archive/2012/04/03/async-in-4-5-worth-the-await.aspx)
-   MSDN Library 中的[异步编程](https://msdn.microsoft.com/library/hh191443.aspx)页
-   [如何使用 Async 构建 ASP.NET Web 应用程序](http://channel9.msdn.com/events/teched/northamerica/2013/dev-b337)(包括提高服务器吞吐量的演示)

## <a name="create-the-model"></a>创建模型

我们将使用[Code First 工作流](~/ef6/modeling/code-first/workflows/new-database.md)来创建模型并生成数据库, 但异步功能适用于所有 ef 模型, 包括使用 EF 设计器创建的那些模型。

-   创建控制台应用程序并将其调用**AsyncDemo**
-   添加 EntityFramework NuGet 包
    -   在解决方案资源管理器中, 右键单击**AsyncDemo**项目
    -   选择 "**管理 NuGet 包 ...** "
    -   在 "管理 NuGet 包" 对话框中, 选择 "**联机**" 选项卡, 然后选择 " **EntityFramework** " 包
    -   单击 "**安装**"
-   添加具有以下实现的**Model.cs**类

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

 

## <a name="create-a-synchronous-program"></a>创建同步程序

现在我们有了 EF 模型, 接下来我们编写一些代码, 用它来执行某些数据访问。

-   将**Program.cs**的内容替换为以下代码

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

此代码调用**PerformDatabaseOperations**方法, 该方法将新的**博客**保存到数据库, 然后从数据库中检索所有**博客**并将其打印到**控制台**。 完成此操作后, 程序将一天的报价写入**控制台**。

由于代码是同步的, 因此, 当我们运行程序时, 可以观察到以下执行流:

1.  **SaveChanges**开始将新**博客**推送到数据库
2.  **SaveChanges**完成
3.  所有**博客**的查询都发送到数据库
4.  查询返回并将结果写入**控制台**
5.  将日报价写入**控制台**

![同步输出](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a>使其异步

现在, 我们已启动并运行程序, 接下来可以开始使用新的 async 和 await 关键字。 我们已对 Program.cs 进行了以下更改

1.  第2行:**System.web**命名空间的 using 语句使我们能够访问 EF async extension 方法。
2.  第4行:用于 " **system.object**命名空间" 命名空间的 using 语句允许我们使用**任务**类型。
3.  第12行 & 18:我们正在捕获作为任务, 用于监视**PerformSomeDatabaseOperations** (第12行) 的进度, 并在程序的所有工作完成后阻止程序执行完成 (第18行)。
4.  第25行:我们更新了**PerformSomeDatabaseOperations** , 将其标记为**async**并返回一个**任务**。
5.  第35行:我们现在正在调用方法的异步版本, 并等待其完成。
6.  第42行:我们现在正在调用 System.linq.enumerable.tolist 的异步版本, 并等待结果。

有关 System.web 命名空间中可用扩展方法的完整列表, 请参阅 QueryableExtensions 类。 *还需要向 using 语句添加 "使用 System.web"。*

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

由于代码是异步的, 因此, 我们可以在运行程序时观察到不同的执行流程:

1.  将命令发送到数据库后, **SaveChanges**开始将*新的**博客**推送到数据库。当前托管线程上不再需要计算时间。**PerformDatabaseOperations**方法返回 (即使尚未执行完毕) 和 Main 方法中的程序流将继续。*
2.  **将日期的 Quote 写入控制台**
     *, 因为在 Main 方法中没有更多的工作要做, 因此, 在数据库操作完成前, 会阻止在等待调用上托管线程。完成后, 我们将执行**PerformDatabaseOperations**的剩余部分。*
3.  **SaveChanges**完成
4.  将对所有**博客**的查询再次发送到*数据库, 在数据库中处理查询时, 托管线程可以随意执行其他工作。由于所有其他执行都已完成, 线程只会在等待调用时停止。*
5.  查询返回并将结果写入**控制台**

![异步输出](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a>要点在于

现在, 我们看到了使用 EF 的异步方法是多么简单。 尽管 async 的优点在简单的控制台应用程序中可能并不是很明显, 但在运行长时间运行或网络绑定的活动可能会阻止应用程序或导致大量线程增加内存占用量。
