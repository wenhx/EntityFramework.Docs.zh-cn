---
title: 连接复原-EF Core
author: rowanmiller
ms.date: 11/15/2016
ms.assetid: e079d4af-c455-4a14-8e15-a8471516d748
uid: core/miscellaneous/connection-resiliency
ms.openlocfilehash: 07646e6ead845c38537945a03367ac7f50784236
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414133"
---
# <a name="connection-resiliency"></a><span data-ttu-id="50739-102">连接复原</span><span class="sxs-lookup"><span data-stu-id="50739-102">Connection Resiliency</span></span>

<span data-ttu-id="50739-103">连接复原将自动重试已失败的数据库命令。</span><span class="sxs-lookup"><span data-stu-id="50739-103">Connection resiliency automatically retries failed database commands.</span></span> <span data-ttu-id="50739-104">通过提供“执行策略”，它封装检测故障，然后重试命令所需的逻辑，该功能可以应用于任何数据库。</span><span class="sxs-lookup"><span data-stu-id="50739-104">The feature can be used with any database by supplying an "execution strategy", which encapsulates the logic necessary to detect failures and retry commands.</span></span> <span data-ttu-id="50739-105">EF Core 提供程序针对特定数据库的失败条件提供定制的执行策略，同时提供最佳的重试策略。</span><span class="sxs-lookup"><span data-stu-id="50739-105">EF Core providers can supply execution strategies tailored to their specific database failure conditions and optimal retry policies.</span></span>

<span data-ttu-id="50739-106">例如，SQL Server 提供程序包括专门针对 SQL Server （包括 SQL Azure）定制的执行策略。</span><span class="sxs-lookup"><span data-stu-id="50739-106">As an example, the SQL Server provider includes an execution strategy that is specifically tailored to SQL Server (including SQL Azure).</span></span> <span data-ttu-id="50739-107">它可以识别可重试的异常类型，并具有可用于最大重试次数、重试间隔时间等的合理默认值。</span><span class="sxs-lookup"><span data-stu-id="50739-107">It is aware of the exception types that can be retried and has sensible defaults for maximum retries, delay between retries, etc.</span></span>

<span data-ttu-id="50739-108">为上下文配置选项时将指定执行策略。</span><span class="sxs-lookup"><span data-stu-id="50739-108">An execution strategy is specified when configuring the options for your context.</span></span> <span data-ttu-id="50739-109">这通常位于派生上下文的 `OnConfiguring` 方法中：</span><span class="sxs-lookup"><span data-stu-id="50739-109">This is typically in the `OnConfiguring` method of your derived context:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#OnConfiguring)]

<span data-ttu-id="50739-110">或在 ASP.NET Core 应用程序的 `Startup.cs` 中：</span><span class="sxs-lookup"><span data-stu-id="50739-110">or in `Startup.cs` for an ASP.NET Core application:</span></span>

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<PicnicContext>(
        options => options.UseSqlServer(
            "<connection string>",
            providerOptions => providerOptions.EnableRetryOnFailure()));
}
```

## <a name="custom-execution-strategy"></a><span data-ttu-id="50739-111">自定义执行策略</span><span class="sxs-lookup"><span data-stu-id="50739-111">Custom execution strategy</span></span>

<span data-ttu-id="50739-112">如果要更改任何默认值，则可以使用一种机制来注册自己的自定义执行策略。</span><span class="sxs-lookup"><span data-stu-id="50739-112">There is a mechanism to register a custom execution strategy of your own if you wish to change any of the defaults.</span></span>

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseMyProvider(
            "<connection string>",
            options => options.ExecutionStrategy(...));
}
```

## <a name="execution-strategies-and-transactions"></a><span data-ttu-id="50739-113">执行策略和事务</span><span class="sxs-lookup"><span data-stu-id="50739-113">Execution strategies and transactions</span></span>

<span data-ttu-id="50739-114">在出现故障时自动重试的执行策略需要能够回滚失败的重试块中的每个操作。</span><span class="sxs-lookup"><span data-stu-id="50739-114">An execution strategy that automatically retries on failures needs to be able to play back each operation in a retry block that fails.</span></span> <span data-ttu-id="50739-115">启用重试后，通过 EF Core 执行的每个操作都将成为其自身的可重试操作。</span><span class="sxs-lookup"><span data-stu-id="50739-115">When retries are enabled, each operation you perform via EF Core becomes its own retriable operation.</span></span> <span data-ttu-id="50739-116">也就是说，如果发生暂时性故障，每个查询和对 `SaveChanges()` 的每个调用都将作为一个单元重试。</span><span class="sxs-lookup"><span data-stu-id="50739-116">That is, each query and each call to `SaveChanges()` will be retried as a unit if a transient failure occurs.</span></span>

<span data-ttu-id="50739-117">但是，如果您的代码使用来启动事务 `BeginTransaction()` 您要定义自己的需要被视为一个单元的一组操作，则需要播放该事务中的所有内容。</span><span class="sxs-lookup"><span data-stu-id="50739-117">However, if your code initiates a transaction using `BeginTransaction()` you are defining your own group of operations that need to be treated as a unit, and everything inside the transaction would need to be played back shall a failure occur.</span></span> <span data-ttu-id="50739-118">如果尝试在使用执行策略时执行此操作，将收到如下所示的异常：</span><span class="sxs-lookup"><span data-stu-id="50739-118">You will receive an exception like the following if you attempt to do this when using an execution strategy:</span></span>

> <span data-ttu-id="50739-119">InvalidOperationException：配置的执行策略 "SqlServerRetryingExecutionStrategy" 不支持用户启动的事务。</span><span class="sxs-lookup"><span data-stu-id="50739-119">InvalidOperationException: The configured execution strategy 'SqlServerRetryingExecutionStrategy' does not support user initiated transactions.</span></span> <span data-ttu-id="50739-120">使用由“DbContext.Database.CreateExecutionStrategy()”返回的执行策略执行事务（作为一个可回溯单元）中的所有操作。</span><span class="sxs-lookup"><span data-stu-id="50739-120">Use the execution strategy returned by 'DbContext.Database.CreateExecutionStrategy()' to execute all the operations in the transaction as a retriable unit.</span></span>

<span data-ttu-id="50739-121">解决方法是使用代表需要执行的所有内容的委托来手动调用执行策略。</span><span class="sxs-lookup"><span data-stu-id="50739-121">The solution is to manually invoke the execution strategy with a delegate representing everything that needs to be executed.</span></span> <span data-ttu-id="50739-122">如果出现暂时性失败，执行策略将再次调用委托。</span><span class="sxs-lookup"><span data-stu-id="50739-122">If a transient failure occurs, the execution strategy will invoke the delegate again.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#ManualTransaction)]

<span data-ttu-id="50739-123">此方法还可用于环境事务。</span><span class="sxs-lookup"><span data-stu-id="50739-123">This approach can also be used with ambient transactions.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#AmbientTransaction)]

## <a name="transaction-commit-failure-and-the-idempotency-issue"></a><span data-ttu-id="50739-124">事务提交失败和幂等性问题</span><span class="sxs-lookup"><span data-stu-id="50739-124">Transaction commit failure and the idempotency issue</span></span>

<span data-ttu-id="50739-125">通常，如果连接失败，当前事务将回滚。</span><span class="sxs-lookup"><span data-stu-id="50739-125">In general, when there is a connection failure the current transaction is rolled back.</span></span> <span data-ttu-id="50739-126">但是，如果在提交事务时断开连接，则事务的生成状态是未知的。</span><span class="sxs-lookup"><span data-stu-id="50739-126">However, if the connection is dropped while the transaction is being committed the resulting state of the transaction is unknown.</span></span> 

<span data-ttu-id="50739-127">默认情况下，执行策略将重试该操作，就像事务已回滚一样，但如果不是这样，则这会导致在新数据库状态不兼容时导致异常，或者如果操作不依赖于特定状态（例如，使用自动生成的键值插入新行时），可能会导致**数据损坏**。</span><span class="sxs-lookup"><span data-stu-id="50739-127">By default, the execution strategy will retry the operation as if the transaction was rolled back, but if it's not the case this will result in an exception if the new database state is incompatible or could lead to **data corruption** if the operation does not rely on a particular state, for example when inserting a new row with auto-generated key values.</span></span>

<span data-ttu-id="50739-128">可以通过多种方式来处理此情况。</span><span class="sxs-lookup"><span data-stu-id="50739-128">There are several ways to deal with this.</span></span>

### <a name="option-1---do-almost-nothing"></a><span data-ttu-id="50739-129">选项 1-执行（几乎不）任何操作</span><span class="sxs-lookup"><span data-stu-id="50739-129">Option 1 - Do (almost) nothing</span></span>

<span data-ttu-id="50739-130">事务提交期间连接失败的可能性很低，因此如果实际发生此情况，应用程序可能会失败。</span><span class="sxs-lookup"><span data-stu-id="50739-130">The likelihood of a connection failure during transaction commit is low so it may be acceptable for your application to just fail if this condition actually occurs.</span></span>

<span data-ttu-id="50739-131">但是，需要避免使用存储生成的密钥，以确保引发异常而不是添加重复的行。</span><span class="sxs-lookup"><span data-stu-id="50739-131">However, you need to avoid using store-generated keys in order to ensure that an exception is thrown instead of adding a duplicate row.</span></span> <span data-ttu-id="50739-132">请考虑使用客户端生成的 GUID 值或客户端值生成器。</span><span class="sxs-lookup"><span data-stu-id="50739-132">Consider using a client-generated GUID value or a client-side value generator.</span></span>

### <a name="option-2---rebuild-application-state"></a><span data-ttu-id="50739-133">选项 2-重建应用程序状态</span><span class="sxs-lookup"><span data-stu-id="50739-133">Option 2 - Rebuild application state</span></span>

1. <span data-ttu-id="50739-134">放弃当前 `DbContext`。</span><span class="sxs-lookup"><span data-stu-id="50739-134">Discard the current `DbContext`.</span></span>
2. <span data-ttu-id="50739-135">创建新 `DbContext`，并从数据库中还原应用程序的状态。</span><span class="sxs-lookup"><span data-stu-id="50739-135">Create a new `DbContext` and restore the state of your application from the database.</span></span>
3. <span data-ttu-id="50739-136">通知用户上次操作可能尚未成功完成。</span><span class="sxs-lookup"><span data-stu-id="50739-136">Inform the user that the last operation might not have been completed successfully.</span></span>

### <a name="option-3---add-state-verification"></a><span data-ttu-id="50739-137">选项 3-添加状态验证</span><span class="sxs-lookup"><span data-stu-id="50739-137">Option 3 - Add state verification</span></span>

<span data-ttu-id="50739-138">对于大多数更改数据库状态的操作，可以添加代码来检查它是否成功。</span><span class="sxs-lookup"><span data-stu-id="50739-138">For most of the operations that change the database state it is possible to add code that checks whether it succeeded.</span></span> <span data-ttu-id="50739-139">EF 提供扩展方法，使其更易于 `IExecutionStrategy.ExecuteInTransaction`。</span><span class="sxs-lookup"><span data-stu-id="50739-139">EF provides an extension method to make this easier - `IExecutionStrategy.ExecuteInTransaction`.</span></span>

<span data-ttu-id="50739-140">此方法将启动并提交事务，并且还接受 `verifySucceeded` 参数中的一个函数，该参数会在事务提交期间发生暂时性错误时调用。</span><span class="sxs-lookup"><span data-stu-id="50739-140">This method begins and commits a transaction and also accepts a function in the `verifySucceeded` parameter that is invoked when a transient error occurs during the transaction commit.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Verification)]

> [!NOTE]
> <span data-ttu-id="50739-141">此处 `SaveChanges` 在 `acceptAllChangesOnSuccess` 设置为 `false` 时调用，以避免 `Unchanged` 成功时将 `Blog` 实体的状态更改为 `SaveChanges`。</span><span class="sxs-lookup"><span data-stu-id="50739-141">Here `SaveChanges` is invoked with `acceptAllChangesOnSuccess` set to `false` to avoid changing the state of the `Blog` entity to `Unchanged` if `SaveChanges` succeeds.</span></span> <span data-ttu-id="50739-142">如果提交失败并且事务已回滚，则允许重试相同的操作。</span><span class="sxs-lookup"><span data-stu-id="50739-142">This allows to retry the same operation if the commit fails and the transaction is rolled back.</span></span>

### <a name="option-4---manually-track-the-transaction"></a><span data-ttu-id="50739-143">选项 4-手动跟踪事务</span><span class="sxs-lookup"><span data-stu-id="50739-143">Option 4 - Manually track the transaction</span></span>

<span data-ttu-id="50739-144">如果需要使用存储生成的密钥或需要一种处理提交失败且不依赖于所执行操作的通用方法，则可以为每个事务分配一个可在提交失败时进行检查的 ID。</span><span class="sxs-lookup"><span data-stu-id="50739-144">If you need to use store-generated keys or need a generic way of handling commit failures that doesn't depend on the operation performed each transaction could be assigned an ID that is checked when the commit fails.</span></span>

1. <span data-ttu-id="50739-145">向数据库添加一个用于跟踪事务状态的表。</span><span class="sxs-lookup"><span data-stu-id="50739-145">Add a table to the database used to track the status of the transactions.</span></span>
2. <span data-ttu-id="50739-146">在每个事务开头的表中插入一行。</span><span class="sxs-lookup"><span data-stu-id="50739-146">Insert a row into the table at the beginning of each transaction.</span></span>
3. <span data-ttu-id="50739-147">如果在提交期间连接失败，请检查数据库中是否存在相应的行。</span><span class="sxs-lookup"><span data-stu-id="50739-147">If the connection fails during the commit, check for the presence of the corresponding row in the database.</span></span>
4. <span data-ttu-id="50739-148">如果提交成功，则删除相应的行以避免表增长。</span><span class="sxs-lookup"><span data-stu-id="50739-148">If the commit is successful, delete the corresponding row to avoid the growth of the table.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Tracking)]

> [!NOTE]
> <span data-ttu-id="50739-149">请确保用于验证的上下文具有定义的执行策略，因为如果在事务提交期间连接失败，则连接可能在验证期间再次失败。</span><span class="sxs-lookup"><span data-stu-id="50739-149">Make sure that the context used for the verification has an execution strategy defined as the connection is likely to fail again during verification if it failed during transaction commit.</span></span>
