---
title: 连接复原和重试逻辑-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 47d68ac1-927e-4842-ab8c-ed8c8698dff2
ms.openlocfilehash: a01216c3399ca4a04943563435eacd0047337a5f
ms.sourcegitcommit: c9c3e00c2d445b784423469838adc071a946e7c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68306578"
---
# <a name="connection-resiliency-and-retry-logic"></a><span data-ttu-id="cff9d-102">连接复原和重试逻辑</span><span class="sxs-lookup"><span data-stu-id="cff9d-102">Connection resiliency and retry logic</span></span>
> [!NOTE]
> <span data-ttu-id="cff9d-103">**仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。</span><span class="sxs-lookup"><span data-stu-id="cff9d-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="cff9d-104">如果使用的是早期版本，则部分或全部信息不适用。</span><span class="sxs-lookup"><span data-stu-id="cff9d-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="cff9d-105">由于后端故障和网络不稳定, 连接到数据库服务器的应用程序始终容易遭受连接中断。</span><span class="sxs-lookup"><span data-stu-id="cff9d-105">Applications connecting to a database server have always been vulnerable to connection breaks due to back-end failures and network instability.</span></span> <span data-ttu-id="cff9d-106">然而, 在基于 LAN 的环境中, 对于专用数据库服务器, 这些错误很少发生, 但通常不需要使用额外的逻辑来处理这些故障。</span><span class="sxs-lookup"><span data-stu-id="cff9d-106">However, in a LAN based environment working against dedicated database servers these errors are rare enough that extra logic to handle those failures is not often required.</span></span> <span data-ttu-id="cff9d-107">随着基于云的数据库服务器 (如 Microsoft Azure SQL 数据库) 的增长以及通过不太可靠的网络进行的连接, 现在更常见的连接中断。</span><span class="sxs-lookup"><span data-stu-id="cff9d-107">With the rise of cloud based database servers such as Windows Azure SQL Database and connections over less reliable networks it is now more common for connection breaks to occur.</span></span> <span data-ttu-id="cff9d-108">这可能是因为云数据库使用防御性技术来确保服务的公平, 如连接限制, 或导致间歇超时和其他暂时性错误的网络不稳定。</span><span class="sxs-lookup"><span data-stu-id="cff9d-108">This could be due to defensive techniques that cloud databases use to ensure fairness of service, such as connection throttling, or to instability in the network causing intermittent timeouts and other transient errors.</span></span>  

<span data-ttu-id="cff9d-109">连接复原是指 EF 自动重试任何由于这些连接中断而失败的命令的功能。</span><span class="sxs-lookup"><span data-stu-id="cff9d-109">Connection Resiliency refers to the ability for EF to automatically retry any commands that fail due to these connection breaks.</span></span>  

## <a name="execution-strategies"></a><span data-ttu-id="cff9d-110">执行策略</span><span class="sxs-lookup"><span data-stu-id="cff9d-110">Execution Strategies</span></span>  

<span data-ttu-id="cff9d-111">连接重试由 IDbExecutionStrategy 接口的实现来处理。</span><span class="sxs-lookup"><span data-stu-id="cff9d-111">Connection retry is taken care of by an implementation of the IDbExecutionStrategy interface.</span></span> <span data-ttu-id="cff9d-112">IDbExecutionStrategy 的实现将负责接受一个操作, 如果发生异常, 则确定重试是否合适, 如果是, 则重试。</span><span class="sxs-lookup"><span data-stu-id="cff9d-112">Implementations of the IDbExecutionStrategy will be responsible for accepting an operation and, if an exception occurs, determining if a retry is appropriate and retrying if it is.</span></span> <span data-ttu-id="cff9d-113">EF 附带四个执行策略:</span><span class="sxs-lookup"><span data-stu-id="cff9d-113">There are four execution strategies that ship with EF:</span></span>  

1. <span data-ttu-id="cff9d-114">**DefaultExecutionStrategy**: 此执行策略不会重试任何操作, 它是 sql server 以外的数据库的默认值。</span><span class="sxs-lookup"><span data-stu-id="cff9d-114">**DefaultExecutionStrategy**: this execution strategy does not retry any operations, it is the default for databases other than sql server.</span></span>  
2. <span data-ttu-id="cff9d-115">**DefaultSqlExecutionStrategy**: 这是默认情况下使用的内部执行策略。</span><span class="sxs-lookup"><span data-stu-id="cff9d-115">**DefaultSqlExecutionStrategy**: this is an internal execution strategy that is used by default.</span></span> <span data-ttu-id="cff9d-116">不过, 这种策略根本不会重试, 它将包装任何可能会暂时性的异常, 以通知用户他们可能想要启用连接复原。</span><span class="sxs-lookup"><span data-stu-id="cff9d-116">This strategy does not retry at all, however, it will wrap any exceptions that could be transient to inform users that they might want to enable connection resiliency.</span></span>  
3. <span data-ttu-id="cff9d-117">**DbExecutionStrategy**: 此类适用于其他执行策略 (包括你自己的自定义) 的基类。</span><span class="sxs-lookup"><span data-stu-id="cff9d-117">**DbExecutionStrategy**: this class is suitable as a base class for other execution strategies, including your own custom ones.</span></span> <span data-ttu-id="cff9d-118">它实现了一个指数重试策略, 其中, 初始重试会出现零延迟, 延迟将以指数方式递增, 直到达到最大重试计数。</span><span class="sxs-lookup"><span data-stu-id="cff9d-118">It implements an exponential retry policy, where the initial retry happens with zero delay and the delay increases exponentially until the maximum retry count is hit.</span></span> <span data-ttu-id="cff9d-119">此类有一个抽象 ShouldRetryOn 方法, 可在派生执行策略中实现该方法, 以控制应重试哪些异常。</span><span class="sxs-lookup"><span data-stu-id="cff9d-119">This class has an abstract ShouldRetryOn method that can be implemented in derived execution strategies to control which exceptions should be retried.</span></span>  
4. <span data-ttu-id="cff9d-120">**SqlAzureExecutionStrategy**: 此执行策略继承自 DbExecutionStrategy, 并将在使用 Azure SQL 数据库时已知可能会暂时性的异常重试。</span><span class="sxs-lookup"><span data-stu-id="cff9d-120">**SqlAzureExecutionStrategy**: this execution strategy inherits from DbExecutionStrategy and will retry on exceptions that are known to be possibly transient when working with Azure SQL Database.</span></span>

> [!NOTE]
> <span data-ttu-id="cff9d-121">执行策略2和4包含在 EF 附带的 Sql Server 提供程序中, 后者位于 EntityFramework 程序集中, 旨在与 SQL Server 配合使用。</span><span class="sxs-lookup"><span data-stu-id="cff9d-121">Execution strategies 2 and 4 are included in the Sql Server provider that ships with EF, which is in the EntityFramework.SqlServer assembly and are designed to work with SQL Server.</span></span>  

## <a name="enabling-an-execution-strategy"></a><span data-ttu-id="cff9d-122">启用执行策略</span><span class="sxs-lookup"><span data-stu-id="cff9d-122">Enabling an Execution Strategy</span></span>  

<span data-ttu-id="cff9d-123">告诉 EF 使用执行策略的最简单方法是使用[DbConfiguration](~/ef6/fundamentals/configuring/code-based.md)类的 SetExecutionStrategy 方法:</span><span class="sxs-lookup"><span data-stu-id="cff9d-123">The easiest way to tell EF to use an execution strategy is with the SetExecutionStrategy method of the [DbConfiguration](~/ef6/fundamentals/configuring/code-based.md) class:</span></span>  

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
    }
}
```  

<span data-ttu-id="cff9d-124">此代码告知 EF 在连接到 SQL Server 时使用 SqlAzureExecutionStrategy。</span><span class="sxs-lookup"><span data-stu-id="cff9d-124">This code tells EF to use the SqlAzureExecutionStrategy when connecting to SQL Server.</span></span>  

## <a name="configuring-the-execution-strategy"></a><span data-ttu-id="cff9d-125">配置执行策略</span><span class="sxs-lookup"><span data-stu-id="cff9d-125">Configuring the Execution Strategy</span></span>  

<span data-ttu-id="cff9d-126">SqlAzureExecutionStrategy 的构造函数可以接受两个参数: MaxRetryCount 和 MaxDelay。</span><span class="sxs-lookup"><span data-stu-id="cff9d-126">The constructor of SqlAzureExecutionStrategy can accept two parameters, MaxRetryCount and MaxDelay.</span></span> <span data-ttu-id="cff9d-127">MaxRetry count 是策略将重试的最大次数。</span><span class="sxs-lookup"><span data-stu-id="cff9d-127">MaxRetry count is the maximum number of times that the strategy will retry.</span></span> <span data-ttu-id="cff9d-128">MaxDelay 是一个 TimeSpan, 表示执行策略将使用的重试之间的最大延迟。</span><span class="sxs-lookup"><span data-stu-id="cff9d-128">The MaxDelay is a TimeSpan representing the maximum delay between retries that the execution strategy will use.</span></span>  

<span data-ttu-id="cff9d-129">若要将最大重试次数设置为 1, 最大延迟为30秒, 请执行以下操作:</span><span class="sxs-lookup"><span data-stu-id="cff9d-129">To set the maximum number of retries to 1 and the maximum delay to 30 seconds you would execute the following:</span></span>  

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

<span data-ttu-id="cff9d-130">第一次发生暂时性故障时, SqlAzureExecutionStrategy 会立即重试, 但会在每次重试之间延迟更长的时间, 直到超过最大重试限制或总时间达到最大延迟。</span><span class="sxs-lookup"><span data-stu-id="cff9d-130">The SqlAzureExecutionStrategy will retry instantly the first time a transient failure occurs, but will delay longer between each retry until either the max retry limit is exceeded or the total time hits the max delay.</span></span>  

<span data-ttu-id="cff9d-131">执行策略只会重试有限数量的异常, 这些异常通常是暂时性的, 你仍需要处理其他错误, 并捕获 RetryLimitExceeded 异常, 以防错误不是暂时性的, 或者需要太长时间才能解决自动.</span><span class="sxs-lookup"><span data-stu-id="cff9d-131">The execution strategies will only retry a limited number of exceptions that are usually transient, you will still need to handle other errors as well as catching the RetryLimitExceeded exception for the case where an error is not transient or takes too long to resolve itself.</span></span>  

<span data-ttu-id="cff9d-132">使用重试执行策略时, 有一些已知的限制:</span><span class="sxs-lookup"><span data-stu-id="cff9d-132">There are some known of limitations when using a retrying execution strategy:</span></span>  

## <a name="streaming-queries-are-not-supported"></a><span data-ttu-id="cff9d-133">不支持流式处理查询</span><span class="sxs-lookup"><span data-stu-id="cff9d-133">Streaming queries are not supported</span></span>  

<span data-ttu-id="cff9d-134">默认情况下, EF6 和更高版本将缓冲查询结果, 而不是流式传输。</span><span class="sxs-lookup"><span data-stu-id="cff9d-134">By default, EF6 and later version will buffer query results rather than streaming them.</span></span> <span data-ttu-id="cff9d-135">如果希望流式传输结果, 可以使用 AsStreaming 方法将 LINQ to Entities 查询更改为流式处理。</span><span class="sxs-lookup"><span data-stu-id="cff9d-135">If you want to have results streamed you can use the AsStreaming method to change a LINQ to Entities query to streaming.</span></span>  

``` csharp
using (var db = new BloggingContext())
{
    var query = (from b in db.Blogs
                orderby b.Url
                select b).AsStreaming();
    }
}
```  

<span data-ttu-id="cff9d-136">注册重试执行策略时, 不支持流式处理。</span><span class="sxs-lookup"><span data-stu-id="cff9d-136">Streaming is not supported when a retrying execution strategy is registered.</span></span> <span data-ttu-id="cff9d-137">存在此限制的原因是, 连接可能会在返回的结果中下降。</span><span class="sxs-lookup"><span data-stu-id="cff9d-137">This limitation exists because the connection could drop part way through the results being returned.</span></span> <span data-ttu-id="cff9d-138">出现这种情况时, EF 需要重新运行整个查询, 但无法可靠地了解已返回的结果 (数据自初始查询发送以来可能已更改, 结果可能会以不同的顺序返回, 结果可能不具有唯一标识符, 等等)。</span><span class="sxs-lookup"><span data-stu-id="cff9d-138">When this occurs, EF needs to re-run the entire query but has no reliable way of knowing which results have already been returned (data may have changed since the initial query was sent, results may come back in a different order, results may not have a unique identifier, etc.).</span></span>  

## <a name="user-initiated-transactions-are-not-supported"></a><span data-ttu-id="cff9d-139">不支持用户启动的事务</span><span class="sxs-lookup"><span data-stu-id="cff9d-139">User initiated transactions are not supported</span></span>  

<span data-ttu-id="cff9d-140">配置导致重试的执行策略时, 对事务的使用存在一些限制。</span><span class="sxs-lookup"><span data-stu-id="cff9d-140">When you have configured an execution strategy that results in retries, there are some limitations around the use of transactions.</span></span>  

<span data-ttu-id="cff9d-141">默认情况下, EF 将在事务中执行任何数据库更新。</span><span class="sxs-lookup"><span data-stu-id="cff9d-141">By default, EF will perform any database updates within a transaction.</span></span> <span data-ttu-id="cff9d-142">不需要执行任何操作来启用此功能, EF 始终会自动执行此操作。</span><span class="sxs-lookup"><span data-stu-id="cff9d-142">You don’t need to do anything to enable this, EF always does this automatically.</span></span>  

<span data-ttu-id="cff9d-143">例如, 在以下代码中, 将在事务中自动执行。</span><span class="sxs-lookup"><span data-stu-id="cff9d-143">For example, in the following code SaveChanges is automatically performed within a transaction.</span></span> <span data-ttu-id="cff9d-144">如果在插入新的一个站点后, SaveChanges 会失败, 则会回滚该事务, 并且不会将更改应用到数据库。</span><span class="sxs-lookup"><span data-stu-id="cff9d-144">If SaveChanges were to fail after inserting one of the new Site’s then the transaction would be rolled back and no changes applied to the database.</span></span> <span data-ttu-id="cff9d-145">上下文还会处于状态, 该状态允许再次调用 SaveChanges 以重试应用更改。</span><span class="sxs-lookup"><span data-stu-id="cff9d-145">The context is also left in a state that allows SaveChanges to be called again to retry applying the changes.</span></span>  

``` csharp
using (var db = new BloggingContext())
{
    db.Blogs.Add(new Site { Url = "http://msdn.com/data/ef" });
    db.Blogs.Add(new Site { Url = "http://blogs.msdn.com/adonet" });
    db.SaveChanges();
}
```  

<span data-ttu-id="cff9d-146">如果不使用重试执行策略, 则可以在单个事务中包装多个操作。</span><span class="sxs-lookup"><span data-stu-id="cff9d-146">When not using a retrying execution strategy you can wrap multiple operations in a single transaction.</span></span> <span data-ttu-id="cff9d-147">例如, 下面的代码将两个 SaveChanges 调用包装在一个事务中。</span><span class="sxs-lookup"><span data-stu-id="cff9d-147">For example, the following code wraps two SaveChanges calls in a single transaction.</span></span> <span data-ttu-id="cff9d-148">如果任一操作的任何部分失败, 则不会应用任何更改。</span><span class="sxs-lookup"><span data-stu-id="cff9d-148">If any part of either operation fails then none of the changes are applied.</span></span>  

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

<span data-ttu-id="cff9d-149">使用重试执行策略时不支持此操作, 因为 EF 不知道任何以前的操作以及如何重试。</span><span class="sxs-lookup"><span data-stu-id="cff9d-149">This is not supported when using a retrying execution strategy because EF isn’t aware of any previous operations and how to retry them.</span></span> <span data-ttu-id="cff9d-150">例如, 如果第二个 SaveChanges 失败, 则 EF 不再具有重试第一个 SaveChanges 调用所需的信息。</span><span class="sxs-lookup"><span data-stu-id="cff9d-150">For example, if the second SaveChanges failed then EF no longer has the required information to retry the first SaveChanges call.</span></span>  

### <a name="workaround-suspend-execution-strategy"></a><span data-ttu-id="cff9d-151">解决方法：挂起执行策略</span><span class="sxs-lookup"><span data-stu-id="cff9d-151">Workaround: Suspend Execution Strategy</span></span>  

<span data-ttu-id="cff9d-152">一种可能的解决方法是针对需要使用用户启动的事务的代码段暂停重试执行策略。</span><span class="sxs-lookup"><span data-stu-id="cff9d-152">One possible workaround is to suspend the retrying execution strategy for the piece of code that needs to use a user initiated transaction.</span></span> <span data-ttu-id="cff9d-153">执行此操作的最简单方法是将 SuspendExecutionStrategy 标志添加到基于代码的配置类, 并更改执行策略 lambda, 以在设置标志时返回默认 (非 retying) 执行策略。</span><span class="sxs-lookup"><span data-stu-id="cff9d-153">The easiest way to do this is to add a SuspendExecutionStrategy flag to your code based configuration class and change the execution strategy lambda to return the default (non-retying) execution strategy when the flag is set.</span></span>  

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

<span data-ttu-id="cff9d-154">请注意, 我们使用 CallContext 存储标志值。</span><span class="sxs-lookup"><span data-stu-id="cff9d-154">Note that we are using CallContext to store the flag value.</span></span> <span data-ttu-id="cff9d-155">这为线程本地存储提供了类似的功能, 但可以安全地使用异步代码 (包括 async query) 并保存实体框架。</span><span class="sxs-lookup"><span data-stu-id="cff9d-155">This provides similar functionality to thread local storage but is safe to use with asynchronous code - including async query and save with Entity Framework.</span></span>  

<span data-ttu-id="cff9d-156">现在, 我们可以挂起使用用户启动的事务的代码部分的执行策略。</span><span class="sxs-lookup"><span data-stu-id="cff9d-156">We can now suspend the execution strategy for the section of code that uses a user initiated transaction.</span></span>  

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

### <a name="workaround-manually-call-execution-strategy"></a><span data-ttu-id="cff9d-157">解决方法：手动调用执行策略</span><span class="sxs-lookup"><span data-stu-id="cff9d-157">Workaround: Manually Call Execution Strategy</span></span>  

<span data-ttu-id="cff9d-158">另一种方法是手动使用执行策略, 并为其分配要运行的整个逻辑集, 以便在其中一个操作失败的情况下可以重试所有操作。</span><span class="sxs-lookup"><span data-stu-id="cff9d-158">Another option is to manually use the execution strategy and give it the entire set of logic to be run, so that it can retry everything if one of the operations fails.</span></span> <span data-ttu-id="cff9d-159">我们仍需要使用上面显示的方法挂起执行策略, 以便可重试代码块中使用的任何上下文都不会尝试重试。</span><span class="sxs-lookup"><span data-stu-id="cff9d-159">We still need to suspend the execution strategy - using the technique shown above - so that any contexts used inside the retryable code block do not attempt to retry.</span></span>  

<span data-ttu-id="cff9d-160">请注意, 应在代码块内构造要重试的所有上下文。</span><span class="sxs-lookup"><span data-stu-id="cff9d-160">Note that any contexts should be constructed within the code block to be retried.</span></span> <span data-ttu-id="cff9d-161">这可确保每次重试都从干净的状态开始。</span><span class="sxs-lookup"><span data-stu-id="cff9d-161">This ensures that we are starting with a clean state for each retry.</span></span>  

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
