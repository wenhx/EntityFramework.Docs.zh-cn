---
title: 连接复原的 EF Core
author: rowanmiller
ms.date: 11/15/2016
ms.assetid: e079d4af-c455-4a14-8e15-a8471516d748
uid: core/miscellaneous/connection-resiliency
ms.openlocfilehash: 729cf9b8c038ea2adba8c79c68d9f6fb1676fefa
ms.sourcegitcommit: 5e11125c9b838ce356d673ef5504aec477321724
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50022179"
---
# <a name="connection-resiliency"></a><span data-ttu-id="ed0a5-102">连接复原</span><span class="sxs-lookup"><span data-stu-id="ed0a5-102">Connection Resiliency</span></span>

<span data-ttu-id="ed0a5-103">连接复原将自动重试已失败的数据库命令。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-103">Connection resiliency automatically retries failed database commands.</span></span> <span data-ttu-id="ed0a5-104">通过提供“执行策略”，它封装检测故障，然后重试命令所需的逻辑，该功能可以应用于任何数据库。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-104">The feature can be used with any database by supplying an "execution strategy", which encapsulates the logic necessary to detect failures and retry commands.</span></span> <span data-ttu-id="ed0a5-105">EF Core 提供程序针对特定数据库的失败条件提供定制的执行策略，同时提供最佳的重试策略。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-105">EF Core providers can supply execution strategies tailored to their specific database failure conditions and optimal retry policies.</span></span>

<span data-ttu-id="ed0a5-106">例如，SQL Server 提供程序包括专门针对 SQL Server （包括 SQL Azure） 的执行策略。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-106">As an example, the SQL Server provider includes an execution strategy that is specifically tailored to SQL Server (including SQL Azure).</span></span> <span data-ttu-id="ed0a5-107">它知道可以重试的异常类型，并且具有合理的默认值的最大重试，重试次数等之间的延迟。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-107">It is aware of the exception types that can be retried and has sensible defaults for maximum retries, delay between retries, etc.</span></span>

<span data-ttu-id="ed0a5-108">为上下文配置选项时将指定执行策略。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-108">An execution strategy is specified when configuring the options for your context.</span></span> <span data-ttu-id="ed0a5-109">这通常位于派生上下文的 `OnConfiguring` 方法中，或位于 ASP.NET Core 应用程序的 `Startup.cs` 中。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-109">This is typically in the `OnConfiguring` method of your derived context, or in `Startup.cs` for an ASP.NET Core application.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#OnConfiguring)]

## <a name="custom-execution-strategy"></a><span data-ttu-id="ed0a5-110">自定义执行策略</span><span class="sxs-lookup"><span data-stu-id="ed0a5-110">Custom execution strategy</span></span>

<span data-ttu-id="ed0a5-111">没有一种机制来注册您如果想要更改任何默认设置自己的自定义执行策略。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-111">There is a mechanism to register a custom execution strategy of your own if you wish to change any of the defaults.</span></span>

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseMyProvider(
            "<connection string>",
            options => options.ExecutionStrategy(...));
}
```

## <a name="execution-strategies-and-transactions"></a><span data-ttu-id="ed0a5-112">执行策略和事务</span><span class="sxs-lookup"><span data-stu-id="ed0a5-112">Execution strategies and transactions</span></span>

<span data-ttu-id="ed0a5-113">在出现故障时自动重试的执行策略需要能够回滚失败的重试块中的每个操作。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-113">An execution strategy that automatically retries on failures needs to be able to play back each operation in a retry block that fails.</span></span> <span data-ttu-id="ed0a5-114">启用重试后，通过 EF Core 执行的每个操作都将成为其自身的可重试操作。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-114">When retries are enabled, each operation you perform via EF Core becomes its own retriable operation.</span></span> <span data-ttu-id="ed0a5-115">也就是说，如果出现暂时性故障，每个查询和对 `SaveChanges()` 的每次调用都将作为一个单元进行重试。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-115">That is, each query and each call to `SaveChanges()` will be retried as a unit if a transient failure occurs.</span></span>

<span data-ttu-id="ed0a5-116">但是，如果你的代码使用 `BeginTransaction()` 启动事务，则你将定义自己的操作组（这些操作需要被视为一个单元），并且如果发生故障，将需要回滚事务内的所有内容。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-116">However, if your code initiates a transaction using `BeginTransaction()` you are defining your own group of operations that need to be treated as a unit, and everything inside the transaction would need to be played back shall a failure occur.</span></span> <span data-ttu-id="ed0a5-117">如果尝试在使用执行策略时执行此操作，将收到如下所示的异常：</span><span class="sxs-lookup"><span data-stu-id="ed0a5-117">You will receive an exception like the following if you attempt to do this when using an execution strategy:</span></span>

> <span data-ttu-id="ed0a5-118">InvalidOperationException： 配置的执行策略 SqlServerRetryingExecutionStrategy 不支持用户启动的事务。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-118">InvalidOperationException: The configured execution strategy 'SqlServerRetryingExecutionStrategy' does not support user initiated transactions.</span></span> <span data-ttu-id="ed0a5-119">使用由“DbContext.Database.CreateExecutionStrategy()”返回的执行策略执行事务（作为一个可回溯单元）中的所有操作。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-119">Use the execution strategy returned by 'DbContext.Database.CreateExecutionStrategy()' to execute all the operations in the transaction as a retriable unit.</span></span>

<span data-ttu-id="ed0a5-120">解决方法是使用代表需要执行的所有内容的委托来手动调用执行策略。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-120">The solution is to manually invoke the execution strategy with a delegate representing everything that needs to be executed.</span></span> <span data-ttu-id="ed0a5-121">如果发生暂时性故障，执行策略将再次调用委托。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-121">If a transient failure occurs, the execution strategy will invoke the delegate again.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#ManualTransaction)]

<span data-ttu-id="ed0a5-122">这种方法还可用于环境事务。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-122">This approach can also be used with ambient transactions.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#AmbientTransaction)]

## <a name="transaction-commit-failure-and-the-idempotency-issue"></a><span data-ttu-id="ed0a5-123">事务提交失败和幂等性问题</span><span class="sxs-lookup"><span data-stu-id="ed0a5-123">Transaction commit failure and the idempotency issue</span></span>

<span data-ttu-id="ed0a5-124">一般情况下，连接失败时当前事务会回滚。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-124">In general, when there is a connection failure the current transaction is rolled back.</span></span> <span data-ttu-id="ed0a5-125">但是，如果在连接断开时在事务正在将提交所生成的事务状态为未知。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-125">However, if the connection is dropped while the transaction is being committed the resulting state of the transaction is unknown.</span></span> <span data-ttu-id="ed0a5-126">请参阅此[博客文章](https://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx)的更多详细信息。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-126">See this [blog post](https://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx) for more details.</span></span>

<span data-ttu-id="ed0a5-127">默认情况下，执行策略将重试该操作，就像该事务已回滚一样。但如果未这样做，当新数据库状态不兼容时，则可能导致异常；或者当操作不依赖于特定状态时（例如使用自动生成的键值插入新行时），则可能导致数据损坏。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-127">By default, the execution strategy will retry the operation as if the transaction was rolled back, but if it's not the case this will result in an exception if the new database state is incompatible or could lead to **data corruption** if the operation does not rely on a particular state, for example when inserting a new row with auto-generated key values.</span></span>

<span data-ttu-id="ed0a5-128">有几种方法来解决此问题。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-128">There are several ways to deal with this.</span></span>

### <a name="option-1---do-almost-nothing"></a><span data-ttu-id="ed0a5-129">选项 1-执行操作 （几乎） 执行任何操作</span><span class="sxs-lookup"><span data-stu-id="ed0a5-129">Option 1 - Do (almost) nothing</span></span>

<span data-ttu-id="ed0a5-130">事务提交期间连接失败的可能性很低，因此如果实际发生此情况，应用程序可能会失败。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-130">The likelihood of a connection failure during transaction commit is low so it may be acceptable for your application to just fail if this condition actually occurs.</span></span>

<span data-ttu-id="ed0a5-131">但是，需要避免使用存储生成的密钥，以确保引发异常而不是添加重复的行。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-131">However, you need to avoid using store-generated keys in order to ensure that an exception is thrown instead of adding a duplicate row.</span></span> <span data-ttu-id="ed0a5-132">请考虑使用客户端生成的 GUID 值或客户端值生成器。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-132">Consider using a client-generated GUID value or a client-side value generator.</span></span>

### <a name="option-2---rebuild-application-state"></a><span data-ttu-id="ed0a5-133">选项 2-重新生成应用程序状态</span><span class="sxs-lookup"><span data-stu-id="ed0a5-133">Option 2 - Rebuild application state</span></span>

1. <span data-ttu-id="ed0a5-134">放弃当前`DbContext`。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-134">Discard the current `DbContext`.</span></span>
2. <span data-ttu-id="ed0a5-135">创建一个新`DbContext`和从数据库中还原应用程序的状态。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-135">Create a new `DbContext` and restore the state of your application from the database.</span></span>
3. <span data-ttu-id="ed0a5-136">通知用户上次操作可能尚未成功完成。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-136">Inform the user that the last operation might not have been completed successfully.</span></span>

### <a name="option-3---add-state-verification"></a><span data-ttu-id="ed0a5-137">选项 3-添加状态验证</span><span class="sxs-lookup"><span data-stu-id="ed0a5-137">Option 3 - Add state verification</span></span>

<span data-ttu-id="ed0a5-138">对于大多数更改数据库状态的操作就可以添加代码来检查它是否成功。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-138">For most of the operations that change the database state it is possible to add code that checks whether it succeeded.</span></span> <span data-ttu-id="ed0a5-139">EF 提供了一个扩展方法来简化此过程- `IExecutionStrategy.ExecuteInTransaction`。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-139">EF provides an extension method to make this easier - `IExecutionStrategy.ExecuteInTransaction`.</span></span>

<span data-ttu-id="ed0a5-140">此方法将开始并提交事务，还将接受在事务提交期间出现暂时性错误时所调用的 `verifySucceeded`参数中的函数。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-140">This method begins and commits a transaction and also accepts a function in the `verifySucceeded` parameter that is invoked when a transient error occurs during the transaction commit.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Verification)]

> [!NOTE]
> <span data-ttu-id="ed0a5-141">这里`SaveChanges`调用使用了`acceptAllChangesOnSuccess`设置为`false`为了避免更改的状态`Blog`到实体`Unchanged`如果`SaveChanges`成功。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-141">Here `SaveChanges` is invoked with `acceptAllChangesOnSuccess` set to `false` to avoid changing the state of the `Blog` entity to `Unchanged` if `SaveChanges` succeeds.</span></span> <span data-ttu-id="ed0a5-142">这样就可以重试相同的操作，如果提交失败并回滚事务。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-142">This allows to retry the same operation if the commit fails and the transaction is rolled back.</span></span>

### <a name="option-4---manually-track-the-transaction"></a><span data-ttu-id="ed0a5-143">选项 4-手动跟踪事务</span><span class="sxs-lookup"><span data-stu-id="ed0a5-143">Option 4 - Manually track the transaction</span></span>

<span data-ttu-id="ed0a5-144">如果需要使用存储生成的密钥或需要一种处理提交失败且不依赖于所执行操作的通用方法，则可以为每个事务分配一个可在提交失败时进行检查的 ID。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-144">If you need to use store-generated keys or need a generic way of handling commit failures that doesn't depend on the operation performed each transaction could be assigned an ID that is checked when the commit fails.</span></span>

1. <span data-ttu-id="ed0a5-145">将表添加到用于跟踪事务的状态的数据库。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-145">Add a table to the database used to track the status of the transactions.</span></span>
2. <span data-ttu-id="ed0a5-146">插入到表中每个事务的开始处的行。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-146">Insert a row into the table at the beginning of each transaction.</span></span>
3. <span data-ttu-id="ed0a5-147">如果连接失败在提交期间，检查存在的数据库中的相应行。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-147">If the connection fails during the commit, check for the presence of the corresponding row in the database.</span></span>
4. <span data-ttu-id="ed0a5-148">如果提交成功，删除对应的行，以避免对表的增长。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-148">If the commit is successful, delete the corresponding row to avoid the growth of the table.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Tracking)]

> [!NOTE]
> <span data-ttu-id="ed0a5-149">请确保用于验证的上下文具有定义的执行策略，因为如果在事务提交期间连接失败，则连接可能在验证期间再次失败。</span><span class="sxs-lookup"><span data-stu-id="ed0a5-149">Make sure that the context used for the verification has an execution strategy defined as the connection is likely to fail again during verification if it failed during transaction commit.</span></span>
