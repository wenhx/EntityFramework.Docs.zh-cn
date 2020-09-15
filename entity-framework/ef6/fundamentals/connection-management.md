---
title: 连接管理-EF6
description: 实体框架6中的连接管理
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/connection-management
ms.openlocfilehash: e2097f5ed0471d309292625990cc5f9a598c5b2a
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070557"
---
# <a name="connection-management"></a><span data-ttu-id="23546-103">连接管理</span><span class="sxs-lookup"><span data-stu-id="23546-103">Connection management</span></span>
<span data-ttu-id="23546-104">本页介绍实体框架与将连接传递到上下文以及 \*\*连接到 ( # B1 \*\* API 的功能的情况。</span><span class="sxs-lookup"><span data-stu-id="23546-104">This page describes the behavior of Entity Framework with regard to passing connections to the context and the functionality of the **Database.Connection.Open()** API.</span></span>  

## <a name="passing-connections-to-the-context"></a><span data-ttu-id="23546-105">将连接传递到上下文</span><span class="sxs-lookup"><span data-stu-id="23546-105">Passing Connections to the Context</span></span>  

### <a name="behavior-for-ef5-and-earlier-versions"></a><span data-ttu-id="23546-106">EF5 及更早版本的行为</span><span class="sxs-lookup"><span data-stu-id="23546-106">Behavior for EF5 and earlier versions</span></span>  

<span data-ttu-id="23546-107">有两个接受连接的构造函数：</span><span class="sxs-lookup"><span data-stu-id="23546-107">There are two constructors which accept connections:</span></span>  

``` csharp
public DbContext(DbConnection existingConnection, bool contextOwnsConnection)
public DbContext(DbConnection existingConnection, DbCompiledModel model, bool contextOwnsConnection)
```  

<span data-ttu-id="23546-108">可以使用这些方法，但必须解决几个限制：</span><span class="sxs-lookup"><span data-stu-id="23546-108">It is possible to use these but you have to work around a couple of limitations:</span></span>  

1. <span data-ttu-id="23546-109">如果将打开的连接传递到其中的任何一个连接，则在框架第一次尝试使用它时，将引发 InvalidOperationException，指出它无法重新打开已打开的连接。</span><span class="sxs-lookup"><span data-stu-id="23546-109">If you pass an open connection to either of these then the first time the framework attempts to use it an InvalidOperationException is thrown saying it cannot re-open an already open connection.</span></span>  
2. <span data-ttu-id="23546-110">ContextOwnsConnection 标志解释为在释放上下文时是否应释放基础存储连接。</span><span class="sxs-lookup"><span data-stu-id="23546-110">The contextOwnsConnection flag is interpreted to mean whether or not the underlying store connection should be disposed when the context is disposed.</span></span> <span data-ttu-id="23546-111">但无论此设置如何，在释放上下文时，存储连接始终关闭。</span><span class="sxs-lookup"><span data-stu-id="23546-111">But, regardless of that setting, the store connection is always closed when the context is disposed.</span></span> <span data-ttu-id="23546-112">因此，如果有多个具有相同连接的 DbContext 在第一次处理上下文时将关闭连接 (同样，如果已使用 DbContext 混合了现有 ADO.NET 连接，DbContext 将始终在) 释放时关闭连接。</span><span class="sxs-lookup"><span data-stu-id="23546-112">So if you have more than one DbContext with the same connection whichever context is disposed first will close the connection (similarly if you have mixed an existing ADO.NET connection with a DbContext, DbContext will always close the connection when it is disposed).</span></span>  

<span data-ttu-id="23546-113">通过传递关闭的连接并仅执行在创建所有上下文后将打开该连接的代码，可以绕过上述首个限制：</span><span class="sxs-lookup"><span data-stu-id="23546-113">It is possible to work around the first limitation above by passing a closed connection and only executing code that would open it once all contexts have been created:</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Common;
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.EntityClient;
using System.Linq;

namespace ConnectionManagementExamples
{
    class ConnectionManagementExampleEF5
    {         
        public static void TwoDbContextsOneConnection()
        {
            using (var context1 = new BloggingContext())
            {
                var conn =
                    ((EntityConnection)  
                        ((IObjectContextAdapter)context1).ObjectContext.Connection)  
                            .StoreConnection;

                using (var context2 = new BloggingContext(conn, contextOwnsConnection: false))
                {
                    context2.Database.ExecuteSqlCommand(
                        @"UPDATE Blogs SET Rating = 5" +
                        " WHERE Name LIKE '%Entity Framework%'");

                    var query = context1.Posts.Where(p => p.Blog.Rating > 5);
                    foreach (var post in query)
                    {
                        post.Title += "[Cool Blog]";
                    }
                    context1.SaveChanges();
                }
            }
        }
    }
}
```  

<span data-ttu-id="23546-114">第二个限制是指您需要避免释放任何 DbContext 对象，直到您已准备好连接才能关闭。</span><span class="sxs-lookup"><span data-stu-id="23546-114">The second limitation just means you need to refrain from disposing any of your DbContext objects until you are ready for the connection to be closed.</span></span>  

### <a name="behavior-in-ef6-and-future-versions"></a><span data-ttu-id="23546-115">EF6 和未来版本中的行为</span><span class="sxs-lookup"><span data-stu-id="23546-115">Behavior in EF6 and future versions</span></span>  

<span data-ttu-id="23546-116">在 EF6 和未来的版本中，DbContext 具有相同的两个构造函数，但不再要求在收到时传递到构造函数的连接被关闭。</span><span class="sxs-lookup"><span data-stu-id="23546-116">In EF6 and future versions the DbContext has the same two constructors but no longer requires that the connection passed to the constructor be closed when it is received.</span></span> <span data-ttu-id="23546-117">现在可以这样做：</span><span class="sxs-lookup"><span data-stu-id="23546-117">So this is now possible:</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
using System.Transactions;

namespace ConnectionManagementExamples
{
    class ConnectionManagementExample
    {
        public static void PassingAnOpenConnection()
        {
            using (var conn = new SqlConnection("{connectionString}"))
            {
                conn.Open();

                var sqlCommand = new SqlCommand();
                sqlCommand.Connection = conn;
                sqlCommand.CommandText =
                    @"UPDATE Blogs SET Rating = 5" +
                     " WHERE Name LIKE '%Entity Framework%'";
                sqlCommand.ExecuteNonQuery();

                using (var context = new BloggingContext(conn, contextOwnsConnection: false))
                {
                    var query = context.Posts.Where(p => p.Blog.Rating > 5);
                    foreach (var post in query)
                    {
                        post.Title += "[Cool Blog]";
                    }
                    context.SaveChanges();
                }

                var sqlCommand2 = new SqlCommand();
                sqlCommand2.Connection = conn;
                sqlCommand2.CommandText =
                    @"UPDATE Blogs SET Rating = 7" +
                     " WHERE Name LIKE '%Entity Framework Rocks%'";
                sqlCommand2.ExecuteNonQuery();
            }
        }
    }
}
```  

<span data-ttu-id="23546-118">此外，contextOwnsConnection 标志现在控制在释放 DbContext 时是否关闭并释放连接。</span><span class="sxs-lookup"><span data-stu-id="23546-118">Also the contextOwnsConnection flag now controls whether or not the connection is both closed and disposed when the DbContext is disposed.</span></span> <span data-ttu-id="23546-119">因此，在上述示例中，当上下文被释放时，连接将不会关闭 (第32行) ，因为它已在 EF 的以前版本中，而是在 (行 40) 释放连接本身。</span><span class="sxs-lookup"><span data-stu-id="23546-119">So in the above example the connection is not closed when the context is disposed (line 32) as it would have been in previous versions of EF, but rather when the connection itself is disposed (line 40).</span></span>  

<span data-ttu-id="23546-120">当然，DbContext 仍有可能控制连接 (只需将 contextOwnsConnection 设置为 true，或者使用其他构造函数之一) 如果需要。</span><span class="sxs-lookup"><span data-stu-id="23546-120">Of course it is still possible for the DbContext to take control of the connection (just set contextOwnsConnection to true or use one of the other constructors) if you so wish.</span></span>  

> [!NOTE]
> <span data-ttu-id="23546-121">在此新模型中使用事务时，还有一些其他注意事项。</span><span class="sxs-lookup"><span data-stu-id="23546-121">There are some additional considerations when using transactions with this new model.</span></span> <span data-ttu-id="23546-122">有关详细信息，请参阅使用 [事务](xref:ef6/saving/transactions)。</span><span class="sxs-lookup"><span data-stu-id="23546-122">For details see [Working with Transactions](xref:ef6/saving/transactions).</span></span>  

## <a name="databaseconnectionopen"></a><span data-ttu-id="23546-123">打开 ( # A1</span><span class="sxs-lookup"><span data-stu-id="23546-123">Database.Connection.Open()</span></span>  

### <a name="behavior-for-ef5-and-earlier-versions"></a><span data-ttu-id="23546-124">EF5 及更早版本的行为</span><span class="sxs-lookup"><span data-stu-id="23546-124">Behavior for EF5 and earlier versions</span></span>  

<span data-ttu-id="23546-125">在 EF5 及更早版本中，存在一个 bug，以便不会更新 **ObjectContext** ，以反映基础存储连接的真实状态。</span><span class="sxs-lookup"><span data-stu-id="23546-125">In EF5 and earlier versions there is a bug such that the **ObjectContext.Connection.State** was not updated to reflect the true state of the underlying store connection.</span></span> <span data-ttu-id="23546-126">例如，如果执行了下面的代码，则可以将状态视为 **已关闭** ，即使基础存储连接已 **打开**。</span><span class="sxs-lookup"><span data-stu-id="23546-126">For example, if you executed the following code you can be returned the status **Closed** even though in fact the underlying store connection is **Open**.</span></span>  

``` csharp
((IObjectContextAdapter)context).ObjectContext.Connection.State
```  

<span data-ttu-id="23546-127">另外，如果您通过调用 ( # A1 打开数据库连接，则在下一次执行查询或调用需要数据库连接的任何内容 (例如，SaveChanges ( # A4 # A5 后，基础存储连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="23546-127">Separately, if you open the database connection by calling Database.Connection.Open() it will be open until the next time you execute a query or call anything which requires a database connection (for example, SaveChanges()) but after that the underlying store connection will be closed.</span></span> <span data-ttu-id="23546-128">然后，上下文将重新打开并在每次需要另一数据库操作时重新关闭连接：</span><span class="sxs-lookup"><span data-stu-id="23546-128">The context will then re-open and re-close the connection any time another database operation is required:</span></span>  

``` csharp
using System;
using System.Data;
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.EntityClient;

namespace ConnectionManagementExamples
{
    public class DatabaseOpenConnectionBehaviorEF5
    {
        public static void DatabaseOpenConnectionBehavior()
        {
            using (var context = new BloggingContext())
            {
                // At this point the underlying store connection is closed

                context.Database.Connection.Open();

                // Now the underlying store connection is open  
                // (though ObjectContext.Connection.State will report closed)

                var blog = new Blog { /* Blog’s properties */ };
                context.Blogs.Add(blog);

                // The underlying store connection is still open  

                context.SaveChanges();

                // After SaveChanges() the underlying store connection is closed  
                // Each SaveChanges() / query etc now opens and immediately closes
                // the underlying store connection

                blog = new Blog { /* Blog’s properties */ };
                context.Blogs.Add(blog);
                context.SaveChanges();
            }
        }
    }
}
```  

### <a name="behavior-in-ef6-and-future-versions"></a><span data-ttu-id="23546-129">EF6 和未来版本中的行为</span><span class="sxs-lookup"><span data-stu-id="23546-129">Behavior in EF6 and future versions</span></span>  

<span data-ttu-id="23546-130">对于 EF6 和更早的版本，如果调用代码选择通过调用上下文来打开连接，我们采用了这种方法。如果打开 ( # A1，则它有很好的理由这样做，框架将假定它要控制连接的打开和关闭，并且将不再自动关闭连接。</span><span class="sxs-lookup"><span data-stu-id="23546-130">For EF6 and future versions we have taken the approach that if the calling code chooses to open the connection by calling context.Database.Connection.Open() then it has a good reason for doing so and the framework will assume that it wants control over opening and closing of the connection and will no longer close the connection automatically.</span></span>  

> [!NOTE]
> <span data-ttu-id="23546-131">这可能会导致连接长时间处于打开状态，因此请谨慎使用。</span><span class="sxs-lookup"><span data-stu-id="23546-131">This can potentially lead to connections which are open for a long time so use with care.</span></span>  

<span data-ttu-id="23546-132">我们还更新了代码，使 ObjectContext 连接状态立即跟踪基础连接的状态。</span><span class="sxs-lookup"><span data-stu-id="23546-132">We also updated the code so that ObjectContext.Connection.State now keeps track of the state of the underlying connection correctly.</span></span>  

``` csharp
using System;
using System.Data;
using System.Data.Entity;
using System.Data.Entity.Core.EntityClient;
using System.Data.Entity.Infrastructure;

namespace ConnectionManagementExamples
{
    internal class DatabaseOpenConnectionBehaviorEF6
    {
        public static void DatabaseOpenConnectionBehavior()
        {
            using (var context = new BloggingContext())
            {
                // At this point the underlying store connection is closed

                context.Database.Connection.Open();

                // Now the underlying store connection is open and the
                // ObjectContext.Connection.State correctly reports open too

                var blog = new Blog { /* Blog’s properties */ };
                context.Blogs.Add(blog);
                context.SaveChanges();

                // The underlying store connection remains open for the next operation  

                blog = new Blog { /* Blog’s properties */ };
                context.Blogs.Add(blog);
                context.SaveChanges();

                // The underlying store connection is still open

           } // The context is disposed – so now the underlying store connection is closed
        }
    }
}
```  
