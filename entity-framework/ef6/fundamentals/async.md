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
# <a name="async-query-and-save"></a>异步查询和保存
> [!NOTE]
> **仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。 如果使用的是早期版本，则部分或全部信息不适用。

EF6 引入了对异步查询的支持，并使用 .NET 4.5 中引入[的异步和等待关键字](https://msdn.microsoft.com/library/vstudio/hh191443.aspx)进行保存。 虽然并非所有应用程序都可能从异步中受益，但它可用于在处理长时间运行、网络或 I/O 绑定任务时提高客户端响应能力和服务器可扩展性。

## <a name="when-to-really-use-async"></a>何时真正使用异步

本演练的目的是以一种易于观察异步和同步程序执行之间的区别的方式介绍异步概念。 本演练的目的不是说明异步编程带来好处的任何关键方案。

异步编程主要侧重于释放当前托管线程（线程运行 .NET 代码），以执行其他工作，同时等待不需要从托管线程进行任何计算时间的操作。 例如，当数据库引擎正在处理查询时，.NET 代码将执行任何操作。

在客户端应用程序（WinForms、WPF 等）中，当前线程可用于执行异步操作时保持 UI 响应。 在服务器应用程序（ASP.NET等）中，线程可用于处理其他传入请求 - 这可以减少内存使用量和/或提高服务器的吞吐量。

在大多数使用异步的应用程序将没有明显的好处，甚至可能是有害的。 在提交特定方案之前，使用测试、分析和常识来测量异步对特定方案的影响。

以下是一些更多有关异步的资源：

-   [布兰登·布雷在 .NET 4.5 中的异步/等待概述](https://devblogs.microsoft.com/dotnet/async-in-4-5-worth-the-await/)
-   MSDN 库中的[异步编程](https://msdn.microsoft.com/library/hh191443.aspx)页面
-   [如何使用 Async 构建ASP.NET Web 应用程序](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337)（包括服务器吞吐量增加的演示）

## <a name="create-the-model"></a>创建模型

我们将使用[Code First 工作流](~/ef6/modeling/code-first/workflows/new-database.md)创建模型并生成数据库，但是异步功能将适用于所有 EF 模型，包括使用 EF 设计器创建的模型。

-   创建控制台应用程序并称之为**AsyncDemo**
-   添加实体框架 NuGet 包
    -   在解决方案资源管理器中，右键单击**AsyncDemo**项目
    -   选择 **"管理 NuGet 包..."...**
    -   在"管理 NuGet 包"对话框中，选择 **"联机**"选项卡并选择 **"实体框架**"包
    -   单击"**安装"**
-   添加具有以下实现**Model.cs**类

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

现在，我们有一个 EF 模型，让我们编写一些代码，用它来执行一些数据访问。

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

此代码调用**执行数据库操作**方法，该方法将新**博客**保存到数据库中，然后从数据库中检索所有**博客**并将其打印到**控制台**。 在此之后，程序将当天的报价写入**控制台**。

由于代码是同步的，因此在运行程序时，我们可以观察到以下执行流：

1.  **保存更改**开始将新**博客**推送到数据库
2.  **保存更改**完成
3.  查询所有**博客**将发送到数据库
4.  查询返回和结果写入**控制台**
5.  当天的报价写入**控制台**

![同步输出](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a>使其异步

现在，我们已经启动和运行我们的程序，我们可以开始使用新的异步和等待关键字。 我们对Program.cs进行了以下更改

1.  第 2 行 **：System.Data.Entity**命名空间的 using 语句允许我们访问 EF 异步扩展方法。
2.  第 4 行：**系统.线程的**using 语句，任务命名空间允许我们使用**任务**类型。
3.  第 12 行 & 18：我们将捕获为任务，用于监视**执行某些数据库操作**（第 12 行）的进度，然后在完成程序的所有工作（第 18 行）后阻止此任务的程序执行完成。
4.  第 25 行：我们更新了**执行一些数据库操作**，以标记为**异步**并返回**任务**。
5.  第 35 行：我们现在正在调用"保存更改"的 Async 版本，等待它完成。
6.  第 42 行：我们现在正在调用 ToList 的 Async 版本，等待结果。

有关 System.Data.Entity 命名空间中可用扩展方法的完整列表，请参阅可查询扩展类。 *您还需要将"使用系统.Data.Entity"添加到您的使用语句中。*

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

现在代码是异步的，我们可以在运行程序时观察到不同的执行流：

1. **保存更改**开始将新**博客**推送到数据库  
    *将命令发送到数据库后，当前托管线程不需要更多的计算时间。**执行数据库操作**方法返回（即使它尚未完成执行），并且 Main 方法中的程序流将继续。*
2. **当天的报价写入控制台**  
    *由于 Main 方法中没有更多工作要做，因此在 Wait 调用上阻止托管线程，直到数据库操作完成。完成后，将执行**其余执行数据库操作**。*
3.  **保存更改**完成  
4.  查询所有**博客**将发送到数据库  
    *同样，托管线程在数据库中处理查询时可以自由地执行其他工作。由于所有其他执行已完成，线程将在"等待"调用时停止。*
5.  查询返回和结果写入**控制台**  

![异步输出](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a>外卖

现在，我们看到使用 EF 的异步方法是多么容易。 尽管异步的优点在简单的控制台应用中可能并不十分明显，但在长时间运行或网络绑定活动可能会阻塞应用程序或导致大量线程增加内存占用的情况下，可以应用这些相同的策略。
