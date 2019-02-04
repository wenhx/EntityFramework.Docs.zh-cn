---
title: 连接复原和重试逻辑-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 47d68ac1-927e-4842-ab8c-ed8c8698dff2
ms.openlocfilehash: 7d6aa870cc32a2b344457fbb04525a7c2c8d1c61
ms.sourcegitcommit: 159c2e9afed7745e7512730ffffaf154bcf2ff4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2019
ms.locfileid: "55668760"
---
# <a name="connection-resiliency-and-retry-logic"></a><span data-ttu-id="2cb15-102">连接复原和重试逻辑</span><span class="sxs-lookup"><span data-stu-id="2cb15-102">Connection resiliency and retry logic</span></span>
> [!NOTE]
> <span data-ttu-id="2cb15-103">**仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。</span><span class="sxs-lookup"><span data-stu-id="2cb15-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="2cb15-104">如果使用的是早期版本，则部分或全部信息不适用。</span><span class="sxs-lookup"><span data-stu-id="2cb15-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="2cb15-105">连接到数据库服务器应用程序始终都容易受到由于后端故障和网络不稳定连接中断。</span><span class="sxs-lookup"><span data-stu-id="2cb15-105">Applications connecting to a database server have always been vulnerable to connection breaks due to back-end failures and network instability.</span></span> <span data-ttu-id="2cb15-106">但是，在基于 LAN 环境中使用专用的数据库服务器上的这些错误很少，额外的逻辑来处理这些故障通常是不需要。</span><span class="sxs-lookup"><span data-stu-id="2cb15-106">However, in a LAN based environment working against dedicated database servers these errors are rare enough that extra logic to handle those failures is not often required.</span></span> <span data-ttu-id="2cb15-107">与云的兴起现在是更常见的连接中断发生不可靠网络上的基于数据库服务器，例如 Windows Azure SQL 数据库和连接。</span><span class="sxs-lookup"><span data-stu-id="2cb15-107">With the rise of cloud based database servers such as Windows Azure SQL Database and connections over less reliable networks it is now more common for connection breaks to occur.</span></span> <span data-ttu-id="2cb15-108">这可能是由于防御技术，云数据库使用，以确保服务，如连接限制，或导致间歇性超时和其他暂时性错误的网络中的不稳定的公平性。</span><span class="sxs-lookup"><span data-stu-id="2cb15-108">This could be due to defensive techniques that cloud databases use to ensure fairness of service, such as connection throttling, or to instability in the network causing intermittent timeouts and other transient errors.</span></span>  

<span data-ttu-id="2cb15-109">连接复原能力是指 EF 会自动重试会由于这些连接中断而失败的任何命令的能力。</span><span class="sxs-lookup"><span data-stu-id="2cb15-109">Connection Resiliency refers to the ability for EF to automatically retry any commands that fail due to these connection breaks.</span></span>  

## <a name="execution-strategies"></a><span data-ttu-id="2cb15-110">执行策略</span><span class="sxs-lookup"><span data-stu-id="2cb15-110">Execution Strategies</span></span>  

<span data-ttu-id="2cb15-111">连接重试是由负责 IDbExecutionStrategy 接口的实现。</span><span class="sxs-lookup"><span data-stu-id="2cb15-111">Connection retry is taken care of by an implementation of the IDbExecutionStrategy interface.</span></span> <span data-ttu-id="2cb15-112">IDbExecutionStrategy 的实现将负责接受操作，如果并发生异常，确定是否适合重试它是否正在重试。</span><span class="sxs-lookup"><span data-stu-id="2cb15-112">Implementations of the IDbExecutionStrategy will be responsible for accepting an operation and, if an exception occurs, determining if a retry is appropriate and retrying if it is.</span></span> <span data-ttu-id="2cb15-113">有四种随 EF 执行策略：</span><span class="sxs-lookup"><span data-stu-id="2cb15-113">There are four execution strategies that ship with EF:</span></span>  

1. <span data-ttu-id="2cb15-114">**DefaultExecutionStrategy**： 此执行策略不会重试任何操作，它是 sql server 以外的数据库的默认值。</span><span class="sxs-lookup"><span data-stu-id="2cb15-114">**DefaultExecutionStrategy**: this execution strategy does not retry any operations, it is the default for databases other than sql server.</span></span>  
2. <span data-ttu-id="2cb15-115">**DefaultSqlExecutionStrategy**： 这是默认情况下使用的内部执行策略。</span><span class="sxs-lookup"><span data-stu-id="2cb15-115">**DefaultSqlExecutionStrategy**: this is an internal execution strategy that is used by default.</span></span> <span data-ttu-id="2cb15-116">此策略不会重根本试，但是，它会换行可能是暂时性通知，告知他们可能想要启用连接复原能力的用户的任何异常。</span><span class="sxs-lookup"><span data-stu-id="2cb15-116">This strategy does not retry at all, however, it will wrap any exceptions that could be transient to inform users that they might want to enable connection resiliency.</span></span>  
3. <span data-ttu-id="2cb15-117">**DbExecutionStrategy**： 此类是适用于其他执行策略，包括你自己的自定义的基类。</span><span class="sxs-lookup"><span data-stu-id="2cb15-117">**DbExecutionStrategy**: this class is suitable as a base class for other execution strategies, including your own custom ones.</span></span> <span data-ttu-id="2cb15-118">它实现的指数重试策略，其中初始重试之前不会发生与零延迟和延迟会增加指数级增长达到最大重试计数。</span><span class="sxs-lookup"><span data-stu-id="2cb15-118">It implements an exponential retry policy, where the initial retry happens with zero delay and the delay increases exponentially until the maximum retry count is hit.</span></span> <span data-ttu-id="2cb15-119">此类具有可以在派生的执行策略以控制哪些异常应重试中实现的抽象 ShouldRetryOn 方法。</span><span class="sxs-lookup"><span data-stu-id="2cb15-119">This class has an abstract ShouldRetryOn method that can be implemented in derived execution strategies to control which exceptions should be retried.</span></span>  
4. <span data-ttu-id="2cb15-120">**SqlAzureExecutionStrategy**： 此执行策略继承自 DbExecutionStrategy，在已知可使用 Azure SQL 数据库时可能是暂时性异常时将重试。</span><span class="sxs-lookup"><span data-stu-id="2cb15-120">**SqlAzureExecutionStrategy**: this execution strategy inherits from DbExecutionStrategy and will retry on exceptions that are known to be possibly transient when working with Azure SQL Database.</span></span>

> [!NOTE]
> <span data-ttu-id="2cb15-121">附带了 EF，位于 EntityFramework.SqlServer 程序集中的 Sql Server 提供程序中包含的执行策略 2 和 4 和用于处理 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="2cb15-121">Execution strategies 2 and 4 are included in the Sql Server provider that ships with EF, which is in the EntityFramework.SqlServer assembly and are designed to work with SQL Server.</span></span>  

## <a name="enabling-an-execution-strategy"></a><span data-ttu-id="2cb15-122">启用执行策略</span><span class="sxs-lookup"><span data-stu-id="2cb15-122">Enabling an Execution Strategy</span></span>  

<span data-ttu-id="2cb15-123">告诉 EF 使用执行策略的最简单方法是使用 SetExecutionStrategy 方法的[DbConfiguration](~/ef6/fundamentals/configuring/code-based.md)类：</span><span class="sxs-lookup"><span data-stu-id="2cb15-123">The easiest way to tell EF to use an execution strategy is with the SetExecutionStrategy method of the [DbConfiguration](~/ef6/fundamentals/configuring/code-based.md) class:</span></span>  

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
    }
}
```  

<span data-ttu-id="2cb15-124">此代码告知 EF 时要使用 SqlAzureExecutionStrategy 连接到 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="2cb15-124">This code tells EF to use the SqlAzureExecutionStrategy when connecting to SQL Server.</span></span>  

## <a name="configuring-the-execution-strategy"></a><span data-ttu-id="2cb15-125">配置执行策略</span><span class="sxs-lookup"><span data-stu-id="2cb15-125">Configuring the Execution Strategy</span></span>  

<span data-ttu-id="2cb15-126">SqlAzureExecutionStrategy 的构造函数可以接受两个参数，MaxRetryCount 和 MaxDelay。</span><span class="sxs-lookup"><span data-stu-id="2cb15-126">The constructor of SqlAzureExecutionStrategy can accept two parameters, MaxRetryCount and MaxDelay.</span></span> <span data-ttu-id="2cb15-127">MaxRetry 计数为该策略将重试的最大次数。</span><span class="sxs-lookup"><span data-stu-id="2cb15-127">MaxRetry count is the maximum number of times that the strategy will retry.</span></span> <span data-ttu-id="2cb15-128">MaxDelay 是表示将使用执行策略的重试之间的最大延迟时间跨度。</span><span class="sxs-lookup"><span data-stu-id="2cb15-128">The MaxDelay is a TimeSpan representing the maximum delay between retries that the execution strategy will use.</span></span>  

<span data-ttu-id="2cb15-129">若要设置重试的最大数目为 1 且为 30 秒的最大延迟应像 execue 以下：</span><span class="sxs-lookup"><span data-stu-id="2cb15-129">To set the maximum number of retries to 1 and the maximum delay to 30 seconds you would execue the following:</span></span>  

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetExecutionStrategy(
            "System.Data.SqlClient",
            () => new SqlAzureExecutionStrategy(1, TimeSpan.FromSeconds(30)));
    }
}
```  

<span data-ttu-id="2cb15-130">第一次一个暂时性的错误发生，但将时间间隔再每次重试，直到任一的最大重试次数限制超过了或的总时间达到的最大延迟时间立即，SqlAzureExecutionStrategy 将重试。</span><span class="sxs-lookup"><span data-stu-id="2cb15-130">The SqlAzureExecutionStrategy will retry instantly the first time a transient failure occurs, but will delay longer between each retry until either the max retry limit is exceeded or the total time hits the max delay.</span></span>  

<span data-ttu-id="2cb15-131">将仅在有限的数量的异常通常是 tansient 重试执行策略，您将仍需要处理其他错误，以及所捕获这种情况的错误不是暂时性或花很长时间来解决 RetryLimitExceeded 异常本身。</span><span class="sxs-lookup"><span data-stu-id="2cb15-131">The execution strategies will only retry a limited number of exceptions that are usually tansient, you will still need to handle other errors as well as catching the RetryLimitExceeded exception for the case where an error is not transient or takes too long to resolve itself.</span></span>  

<span data-ttu-id="2cb15-132">使用重试执行策略时，有一些已知限制：</span><span class="sxs-lookup"><span data-stu-id="2cb15-132">There are some known of limitations when using a retrying execution strategy:</span></span>  

## <a name="streaming-queries-are-not-supported"></a><span data-ttu-id="2cb15-133">不支持流式处理查询</span><span class="sxs-lookup"><span data-stu-id="2cb15-133">Streaming queries are not supported</span></span>  

<span data-ttu-id="2cb15-134">默认情况下，EF6 和更高版本进行缓冲处理的查询结果，而不是流式处理它们。</span><span class="sxs-lookup"><span data-stu-id="2cb15-134">By default, EF6 and later version will buffer query results rather than streaming them.</span></span> <span data-ttu-id="2cb15-135">如果想要的结果流式传输你可以使用 AsStreaming 方法更改 LINQ to Entities 查询到流式处理。</span><span class="sxs-lookup"><span data-stu-id="2cb15-135">If you want to have results streamed you can use the AsStreaming method to change a LINQ to Entities query to streaming.</span></span>  

``` csharp
using (var db = new BloggingContext())
{
    var query = (from b in db.Blogs
                orderby b.Url
                select b).AsStreaming();
    }
}
```  

<span data-ttu-id="2cb15-136">注册正在重试执行策略时，不支持流式处理。</span><span class="sxs-lookup"><span data-stu-id="2cb15-136">Streaming is not supported when a retrying execution strategy is registered.</span></span> <span data-ttu-id="2cb15-137">存在此限制，因为连接无法期间删除通过所返回的结果。</span><span class="sxs-lookup"><span data-stu-id="2cb15-137">This limitation exists because the connection could drop part way through the results being returned.</span></span> <span data-ttu-id="2cb15-138">此操作时，EF 需要重新运行整个查询，但具有可靠的方法知道已返回的结果 （数据可能已更改自初始查询的发送，结果可能会返回不同的顺序，结果可能不具有唯一标识符等等。)。</span><span class="sxs-lookup"><span data-stu-id="2cb15-138">When this occurs, EF needs to re-run the entire query but has no reliable way of knowing which results have already been returned (data may have changed since the initial query was sent, results may come back in a different order, results may not have a unique identifier, etc.).</span></span>  

## <a name="user-initiated-transactions-are-not-supported"></a><span data-ttu-id="2cb15-139">不支持用户启动事务</span><span class="sxs-lookup"><span data-stu-id="2cb15-139">User initiated transactions are not supported</span></span>  

<span data-ttu-id="2cb15-140">已配置导致重试执行策略，时使用的事务存在一些限制。</span><span class="sxs-lookup"><span data-stu-id="2cb15-140">When you have configured an execution strategy that results in retries, there are some limitations around the use of transactions.</span></span>  

<span data-ttu-id="2cb15-141">默认情况下，EF 将执行的事务中任何数据库更新。</span><span class="sxs-lookup"><span data-stu-id="2cb15-141">By default, EF will perform any database updates within a transaction.</span></span> <span data-ttu-id="2cb15-142">无需执行任何操作即可启用此功能，EF 始终自动执行此操作。</span><span class="sxs-lookup"><span data-stu-id="2cb15-142">You don’t need to do anything to enable this, EF always does this automatically.</span></span>  

<span data-ttu-id="2cb15-143">例如，下面的代码中 SaveChanges 自动在事务内执行。</span><span class="sxs-lookup"><span data-stu-id="2cb15-143">For example, in the following code SaveChanges is automatically performed within a transaction.</span></span> <span data-ttu-id="2cb15-144">如果 SaveChanges 后插入一个新的站点，则会回滚事务并不更改应用到数据库失败。</span><span class="sxs-lookup"><span data-stu-id="2cb15-144">If SaveChanges were to fail after inserting one of the new Site’s then the transaction would be rolled back and no changes applied to the database.</span></span> <span data-ttu-id="2cb15-145">上下文还处于允许 SaveChanges 以再次调用以重试将更改应用的状态。</span><span class="sxs-lookup"><span data-stu-id="2cb15-145">The context is also left in a state that allows SaveChanges to be called again to retry applying the changes.</span></span>  

``` csharp
using (var db = new BloggingContext())
{
    db.Blogs.Add(new Site { Url = "http://msdn.com/data/ef" });
    db.Blogs.Add(new Site { Url = "http://blogs.msdn.com/adonet" });
    db.SaveChanges();
}
```  

<span data-ttu-id="2cb15-146">不使用重试执行策略时可以在单个事务中包装多个操作。</span><span class="sxs-lookup"><span data-stu-id="2cb15-146">When not using a retrying execution strategy you can wrap multiple operations in a single transaction.</span></span> <span data-ttu-id="2cb15-147">例如，下面的代码将两个 SaveChanges 调用包装在单个事务中。</span><span class="sxs-lookup"><span data-stu-id="2cb15-147">For example, the following code wraps two SaveChanges calls in a single transaction.</span></span> <span data-ttu-id="2cb15-148">如果这两种操作的任何部分然后失败的任何更改会应用。</span><span class="sxs-lookup"><span data-stu-id="2cb15-148">If any part of either operation fails then none of the changes are applied.</span></span>  

``` csharp
using (var db = new BloggingContext())
{
    using (var trn = db.Database.BeginTransaction())
    {
        db.Blogs.Add(new Site { Url = "http://msdn.com/data/ef" });
        db.Blogs.Add(new Site { Url = "http://blogs.msdn.com/adonet" });
        db.SaveChanges();

        db.Blogs.Add(new Site { Url = "http://twitter.com/efmagicunicorns" });
        db.SaveChanges();

        trn.Commit();
    }
}
```  

<span data-ttu-id="2cb15-149">仅使用重试执行策略，因为 EF 不知道的任何先前的操作以及如何重试时，不支持此操作。</span><span class="sxs-lookup"><span data-stu-id="2cb15-149">This is not supported when using a retrying execution strategy because EF isn’t aware of any previous operations and how to retry them.</span></span> <span data-ttu-id="2cb15-150">例如，如果第二个 SaveChanges 未通过，则 EF 无法再具有所需的信息以第一次的 SaveChanges 调用重试。</span><span class="sxs-lookup"><span data-stu-id="2cb15-150">For example, if the second SaveChanges failed then EF no longer has the required information to retry the first SaveChanges call.</span></span>  

### <a name="workaround-suspend-execution-strategy"></a><span data-ttu-id="2cb15-151">解决方法：挂起执行策略</span><span class="sxs-lookup"><span data-stu-id="2cb15-151">Workaround: Suspend Execution Strategy</span></span>  

<span data-ttu-id="2cb15-152">一种解决方法是代码的挂起的需要使用用户段的重试执行策略启动的事务。</span><span class="sxs-lookup"><span data-stu-id="2cb15-152">One possible workaround is to suspend the retrying execution strategy for the piece of code that needs to use a user initiated transaction.</span></span> <span data-ttu-id="2cb15-153">若要执行此操作的最简单方法是添加到你的代码的 SuspendExecutionStrategy 标志基于配置类和更改执行策略 lambda 时要返回的默认 (非 retying) 执行策略设置的标志。</span><span class="sxs-lookup"><span data-stu-id="2cb15-153">The easiest way to do this is to add a SuspendExecutionStrategy flag to your code based configuration class and change the execution strategy lambda to return the default (non-retying) execution strategy when the flag is set.</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.SqlServer;
using System.Runtime.Remoting.Messaging;

namespace Demo
{
    public class MyConfiguration : DbConfiguration
    {
        public MyConfiguration()
        {
            this.SetExecutionStrategy("System.Data.SqlClient", () => SuspendExecutionStrategy
              ? (IDbExecutionStrategy)new DefaultExecutionStrategy()
              : new SqlAzureExecutionStrategy());
        }

        public static bool SuspendExecutionStrategy
        {
            get
            {
                return (bool?)CallContext.LogicalGetData("SuspendExecutionStrategy") ?? false;
            }
            set
            {
                CallContext.LogicalSetData("SuspendExecutionStrategy", value);
            }
        }
    }
}
```  

<span data-ttu-id="2cb15-154">请注意，我们将使用 CallContext 来存储标志值。</span><span class="sxs-lookup"><span data-stu-id="2cb15-154">Note that we are using CallContext to store the flag value.</span></span> <span data-ttu-id="2cb15-155">这提供了线程本地存储与类似的功能，但安全地使用与异步代码-包括异步查询和保存与实体框架。</span><span class="sxs-lookup"><span data-stu-id="2cb15-155">This provides similar functionality to thread local storage but is safe to use with asynchronous code - including async query and save with Entity Framework.</span></span>  

<span data-ttu-id="2cb15-156">我们现在可以暂停使用用户启动的事务的代码部分的执行策略。</span><span class="sxs-lookup"><span data-stu-id="2cb15-156">We can now suspend the execution strategy for the section of code that uses a user initiated transaction.</span></span>  

``` csharp
using (var db = new BloggingContext())
{
    MyConfiguration.SuspendExecutionStrategy = true;

    using (var trn = db.Database.BeginTransaction())
    {
        db.Blogs.Add(new Blog { Url = "http://msdn.com/data/ef" });
        db.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/adonet" });
        db.SaveChanges();

        db.Blogs.Add(new Blog { Url = "http://twitter.com/efmagicunicorns" });
        db.SaveChanges();

        trn.Commit();
    }

    MyConfiguration.SuspendExecutionStrategy = false;
}
```  

### <a name="workaround-manually-call-execution-strategy"></a><span data-ttu-id="2cb15-157">解决方法：手动调用执行策略</span><span class="sxs-lookup"><span data-stu-id="2cb15-157">Workaround: Manually Call Execution Strategy</span></span>  

<span data-ttu-id="2cb15-158">另一个选项是手动使用执行策略并为其提供逻辑来运行，整个集，以便它可以重试的所有内容如果其中一个操作失败。</span><span class="sxs-lookup"><span data-stu-id="2cb15-158">Another option is to manually use the execution strategy and give it the entire set of logic to be run, so that it can retry everything if one of the operations fails.</span></span> <span data-ttu-id="2cb15-159">我们仍需要挂起执行策略的使用方法，以便在可重试代码块内使用任何上下文不尝试重试-上面所示。</span><span class="sxs-lookup"><span data-stu-id="2cb15-159">We still need to suspend the execution strategy - using the technique shown above - so that any contexts used inside the retryable code block do not attempt to retry.</span></span>  

<span data-ttu-id="2cb15-160">请注意，应在代码块中将重试构造任何上下文。</span><span class="sxs-lookup"><span data-stu-id="2cb15-160">Note that any contexts should be constructed within the code block to be retried.</span></span> <span data-ttu-id="2cb15-161">这可确保我们开始使用干净状态，以便每次重试。</span><span class="sxs-lookup"><span data-stu-id="2cb15-161">This ensures that we are starting with a clean state for each retry.</span></span>  

``` csharp
var executionStrategy = new SqlAzureExecutionStrategy();

MyConfiguration.SuspendExecutionStrategy = true;

executionStrategy.Execute(
    () =>
    {
        using (var db = new BloggingContext())
        {
            using (var trn = db.Database.BeginTransaction())
            {
                db.Blogs.Add(new Blog { Url = "http://msdn.com/data/ef" });
                db.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/adonet" });
                db.SaveChanges();

                db.Blogs.Add(new Blog { Url = "http://twitter.com/efmagicunicorns" });
                db.SaveChanges();

                trn.Commit();
            }
        }
    });

MyConfiguration.SuspendExecutionStrategy = false;
```  
