---
title: 连接复原和重试逻辑-EF6
description: 实体框架6中的连接复原和重试逻辑
author: AndriySvyryd
ms.date: 11/20/2019
ms.assetid: 47d68ac1-927e-4842-ab8c-ed8c8698dff2
uid: ef6/fundamentals/connection-resiliency/retry-logic
ms.openlocfilehash: 7d05c924f309e410bc457b7e46b0618d38c95569
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616111"
---
# <a name="connection-resiliency-and-retry-logic"></a><span data-ttu-id="7b7ad-103">连接复原和重试逻辑</span><span class="sxs-lookup"><span data-stu-id="7b7ad-103">Connection resiliency and retry logic</span></span>
> [!NOTE]
> <span data-ttu-id="7b7ad-104">**仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="7b7ad-105">如果使用的是早期版本，则部分或全部信息不适用。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="7b7ad-106">由于后端故障和网络不稳定，连接到数据库服务器的应用程序始终容易遭受连接中断。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-106">Applications connecting to a database server have always been vulnerable to connection breaks due to back-end failures and network instability.</span></span> <span data-ttu-id="7b7ad-107">然而，在基于 LAN 的环境中，对于专用数据库服务器，这些错误很少发生，但通常不需要使用额外的逻辑来处理这些故障。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-107">However, in a LAN based environment working against dedicated database servers these errors are rare enough that extra logic to handle those failures is not often required.</span></span> <span data-ttu-id="7b7ad-108">随着基于云的数据库服务器（如 Microsoft Azure SQL 数据库）的增长以及通过不太可靠的网络进行的连接，现在更常见的连接中断。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-108">With the rise of cloud based database servers such as Windows Azure SQL Database and connections over less reliable networks it is now more common for connection breaks to occur.</span></span> <span data-ttu-id="7b7ad-109">这可能是因为云数据库使用防御性技术来确保服务的公平，如连接限制，或导致间歇超时和其他暂时性错误的网络不稳定。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-109">This could be due to defensive techniques that cloud databases use to ensure fairness of service, such as connection throttling, or to instability in the network causing intermittent timeouts and other transient errors.</span></span>  

<span data-ttu-id="7b7ad-110">连接复原是指 EF 自动重试任何由于这些连接中断而失败的命令的功能。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-110">Connection Resiliency refers to the ability for EF to automatically retry any commands that fail due to these connection breaks.</span></span>  

## <a name="execution-strategies"></a><span data-ttu-id="7b7ad-111">执行策略</span><span class="sxs-lookup"><span data-stu-id="7b7ad-111">Execution Strategies</span></span>  

<span data-ttu-id="7b7ad-112">连接重试由 IDbExecutionStrategy 接口的实现来处理。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-112">Connection retry is taken care of by an implementation of the IDbExecutionStrategy interface.</span></span> <span data-ttu-id="7b7ad-113">IDbExecutionStrategy 的实现将负责接受一个操作，如果发生异常，则确定重试是否合适，如果是，则重试。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-113">Implementations of the IDbExecutionStrategy will be responsible for accepting an operation and, if an exception occurs, determining if a retry is appropriate and retrying if it is.</span></span> <span data-ttu-id="7b7ad-114">EF 附带四个执行策略：</span><span class="sxs-lookup"><span data-stu-id="7b7ad-114">There are four execution strategies that ship with EF:</span></span>  

1. <span data-ttu-id="7b7ad-115">**DefaultExecutionStrategy**：此执行策略不会重试任何操作，它是 sql server 以外的数据库的默认值。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-115">**DefaultExecutionStrategy**: this execution strategy does not retry any operations, it is the default for databases other than sql server.</span></span>  
2. <span data-ttu-id="7b7ad-116">**DefaultSqlExecutionStrategy**：这是默认情况下使用的内部执行策略。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-116">**DefaultSqlExecutionStrategy**: this is an internal execution strategy that is used by default.</span></span> <span data-ttu-id="7b7ad-117">不过，这种策略根本不会重试，它将包装任何可能会暂时性的异常，以通知用户他们可能想要启用连接复原。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-117">This strategy does not retry at all, however, it will wrap any exceptions that could be transient to inform users that they might want to enable connection resiliency.</span></span>  
3. <span data-ttu-id="7b7ad-118">**DbExecutionStrategy**：此类适用于其他执行策略（包括你自己的自定义）的基类。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-118">**DbExecutionStrategy**: this class is suitable as a base class for other execution strategies, including your own custom ones.</span></span> <span data-ttu-id="7b7ad-119">它实现了一个指数重试策略，其中，初始重试会出现零延迟，延迟将以指数方式递增，直到达到最大重试计数。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-119">It implements an exponential retry policy, where the initial retry happens with zero delay and the delay increases exponentially until the maximum retry count is hit.</span></span> <span data-ttu-id="7b7ad-120">此类有一个抽象 ShouldRetryOn 方法，可在派生执行策略中实现该方法，以控制应重试哪些异常。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-120">This class has an abstract ShouldRetryOn method that can be implemented in derived execution strategies to control which exceptions should be retried.</span></span>  
4. <span data-ttu-id="7b7ad-121">**SqlAzureExecutionStrategy**：此执行策略继承自 DbExecutionStrategy，并将在使用 Azure SQL 数据库时已知可能会暂时性的异常重试。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-121">**SqlAzureExecutionStrategy**: this execution strategy inherits from DbExecutionStrategy and will retry on exceptions that are known to be possibly transient when working with Azure SQL Database.</span></span>

> [!NOTE]
> <span data-ttu-id="7b7ad-122">执行策略2和4包含在 EF 附带的 Sql Server 提供程序中，后者位于 EntityFramework 程序集中，旨在与 SQL Server 配合使用。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-122">Execution strategies 2 and 4 are included in the Sql Server provider that ships with EF, which is in the EntityFramework.SqlServer assembly and are designed to work with SQL Server.</span></span>  

## <a name="enabling-an-execution-strategy"></a><span data-ttu-id="7b7ad-123">启用执行策略</span><span class="sxs-lookup"><span data-stu-id="7b7ad-123">Enabling an Execution Strategy</span></span>  

<span data-ttu-id="7b7ad-124">告诉 EF 使用执行策略的最简单方法是使用 [DbConfiguration](xref:ef6/fundamentals/configuring/code-based) 类的 SetExecutionStrategy 方法：</span><span class="sxs-lookup"><span data-stu-id="7b7ad-124">The easiest way to tell EF to use an execution strategy is with the SetExecutionStrategy method of the [DbConfiguration](xref:ef6/fundamentals/configuring/code-based) class:</span></span>  

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
    }
}
```  

<span data-ttu-id="7b7ad-125">此代码告知 EF 在连接到 SQL Server 时使用 SqlAzureExecutionStrategy。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-125">This code tells EF to use the SqlAzureExecutionStrategy when connecting to SQL Server.</span></span>  

## <a name="configuring-the-execution-strategy"></a><span data-ttu-id="7b7ad-126">配置执行策略</span><span class="sxs-lookup"><span data-stu-id="7b7ad-126">Configuring the Execution Strategy</span></span>  

<span data-ttu-id="7b7ad-127">SqlAzureExecutionStrategy 的构造函数可以接受两个参数： MaxRetryCount 和 MaxDelay。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-127">The constructor of SqlAzureExecutionStrategy can accept two parameters, MaxRetryCount and MaxDelay.</span></span> <span data-ttu-id="7b7ad-128">MaxRetry count 是策略将重试的最大次数。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-128">MaxRetry count is the maximum number of times that the strategy will retry.</span></span> <span data-ttu-id="7b7ad-129">MaxDelay 是一个 TimeSpan，表示执行策略将使用的重试之间的最大延迟。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-129">The MaxDelay is a TimeSpan representing the maximum delay between retries that the execution strategy will use.</span></span>  

<span data-ttu-id="7b7ad-130">若要将最大重试次数设置为1，最大延迟为30秒，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="7b7ad-130">To set the maximum number of retries to 1 and the maximum delay to 30 seconds you would execute the following:</span></span>  

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

<span data-ttu-id="7b7ad-131">第一次发生暂时性故障时，SqlAzureExecutionStrategy 会立即重试，但会在每次重试之间延迟更长的时间，直到超过最大重试限制或总时间达到最大延迟。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-131">The SqlAzureExecutionStrategy will retry instantly the first time a transient failure occurs, but will delay longer between each retry until either the max retry limit is exceeded or the total time hits the max delay.</span></span>  

<span data-ttu-id="7b7ad-132">执行策略只会重试有限数量的异常，这些异常通常是暂时性的，你仍需要处理其他错误，并捕获 RetryLimitExceeded 异常，以防错误不是暂时性的，或者因错误而无法处理。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-132">The execution strategies will only retry a limited number of exceptions that are usually transient, you will still need to handle other errors as well as catching the RetryLimitExceeded exception for the case where an error is not transient or takes too long to resolve itself.</span></span>  

<span data-ttu-id="7b7ad-133">使用重试执行策略时，有一些已知的限制：</span><span class="sxs-lookup"><span data-stu-id="7b7ad-133">There are some known of limitations when using a retrying execution strategy:</span></span>  

## <a name="streaming-queries-are-not-supported"></a><span data-ttu-id="7b7ad-134">不支持流式处理查询</span><span class="sxs-lookup"><span data-stu-id="7b7ad-134">Streaming queries are not supported</span></span>  

<span data-ttu-id="7b7ad-135">默认情况下，EF6 和更高版本将缓冲查询结果，而不是流式传输。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-135">By default, EF6 and later version will buffer query results rather than streaming them.</span></span> <span data-ttu-id="7b7ad-136">如果希望流式传输结果，可以使用 AsStreaming 方法将 LINQ to Entities 查询更改为流式处理。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-136">If you want to have results streamed you can use the AsStreaming method to change a LINQ to Entities query to streaming.</span></span>  

``` csharp
using (var db = new BloggingContext())
{
    var query = (from b in db.Blogs
                orderby b.Url
                select b).AsStreaming();
    }
}
```  

<span data-ttu-id="7b7ad-137">注册重试执行策略时，不支持流式处理。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-137">Streaming is not supported when a retrying execution strategy is registered.</span></span> <span data-ttu-id="7b7ad-138">存在此限制的原因是，连接可能会在返回的结果中下降。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-138">This limitation exists because the connection could drop part way through the results being returned.</span></span> <span data-ttu-id="7b7ad-139">发生这种情况时，EF 需要重新运行整个查询，但不能通过可靠的方式知道哪些结果已返回 (数据自初始查询发送以来可能已更改，结果可能会以不同的顺序返回，结果可能不具有唯一标识符，等等 ) 。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-139">When this occurs, EF needs to re-run the entire query but has no reliable way of knowing which results have already been returned (data may have changed since the initial query was sent, results may come back in a different order, results may not have a unique identifier, etc.).</span></span>  

## <a name="user-initiated-transactions-are-not-supported"></a><span data-ttu-id="7b7ad-140">不支持用户启动的事务</span><span class="sxs-lookup"><span data-stu-id="7b7ad-140">User initiated transactions are not supported</span></span>  

<span data-ttu-id="7b7ad-141">配置导致重试的执行策略时，对事务的使用存在一些限制。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-141">When you have configured an execution strategy that results in retries, there are some limitations around the use of transactions.</span></span>  

<span data-ttu-id="7b7ad-142">默认情况下，EF 将在事务中执行任何数据库更新。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-142">By default, EF will perform any database updates within a transaction.</span></span> <span data-ttu-id="7b7ad-143">不需要执行任何操作来启用此功能，EF 始终会自动执行此操作。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-143">You don’t need to do anything to enable this, EF always does this automatically.</span></span>  

<span data-ttu-id="7b7ad-144">例如，在以下代码中，将在事务中自动执行。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-144">For example, in the following code SaveChanges is automatically performed within a transaction.</span></span> <span data-ttu-id="7b7ad-145">如果在插入新的一个站点后，SaveChanges 会失败，则会回滚该事务，并且不会将更改应用到数据库。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-145">If SaveChanges were to fail after inserting one of the new Site’s then the transaction would be rolled back and no changes applied to the database.</span></span> <span data-ttu-id="7b7ad-146">上下文还会处于状态，该状态允许再次调用 SaveChanges 以重试应用更改。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-146">The context is also left in a state that allows SaveChanges to be called again to retry applying the changes.</span></span>  

``` csharp
using (var db = new BloggingContext())
{
    db.Blogs.Add(new Site { Url = "http://msdn.com/data/ef" });
    db.Blogs.Add(new Site { Url = "http://blogs.msdn.com/adonet" });
    db.SaveChanges();
}
```  

<span data-ttu-id="7b7ad-147">如果不使用重试执行策略，则可以在单个事务中包装多个操作。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-147">When not using a retrying execution strategy you can wrap multiple operations in a single transaction.</span></span> <span data-ttu-id="7b7ad-148">例如，下面的代码将两个 SaveChanges 调用包装在一个事务中。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-148">For example, the following code wraps two SaveChanges calls in a single transaction.</span></span> <span data-ttu-id="7b7ad-149">如果任一操作的任何部分失败，则不会应用任何更改。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-149">If any part of either operation fails then none of the changes are applied.</span></span>  

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

<span data-ttu-id="7b7ad-150">使用重试执行策略时不支持此操作，因为 EF 不知道任何以前的操作以及如何重试。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-150">This is not supported when using a retrying execution strategy because EF isn’t aware of any previous operations and how to retry them.</span></span> <span data-ttu-id="7b7ad-151">例如，如果第二个 SaveChanges 失败，则 EF 不再具有重试第一个 SaveChanges 调用所需的信息。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-151">For example, if the second SaveChanges failed then EF no longer has the required information to retry the first SaveChanges call.</span></span>  

### <a name="solution-manually-call-execution-strategy"></a><span data-ttu-id="7b7ad-152">解决方案：手动调用执行策略</span><span class="sxs-lookup"><span data-stu-id="7b7ad-152">Solution: Manually Call Execution Strategy</span></span>  

<span data-ttu-id="7b7ad-153">解决方案是手动使用执行策略，并为其分配要运行的整个逻辑集，以便在其中一个操作失败的情况下可以重试所有操作。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-153">The solution is to manually use the execution strategy and give it the entire set of logic to be run, so that it can retry everything if one of the operations fails.</span></span> <span data-ttu-id="7b7ad-154">当派生自 DbExecutionStrategy 的执行策略正在运行时，它将挂起 SaveChanges 中使用的隐式执行策略。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-154">When an execution strategy derived from DbExecutionStrategy is running it will suspend the implicit execution strategy used in SaveChanges.</span></span>  

<span data-ttu-id="7b7ad-155">请注意，应在代码块内构造要重试的所有上下文。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-155">Note that any contexts should be constructed within the code block to be retried.</span></span> <span data-ttu-id="7b7ad-156">这可确保每次重试都从干净的状态开始。</span><span class="sxs-lookup"><span data-stu-id="7b7ad-156">This ensures that we are starting with a clean state for each retry.</span></span>  

``` csharp
var executionStrategy = new SqlAzureExecutionStrategy();

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
```  
