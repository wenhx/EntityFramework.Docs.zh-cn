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
# <a name="connection-resiliency"></a>连接复原

连接复原将自动重试已失败的数据库命令。 通过提供“执行策略”，它封装检测故障，然后重试命令所需的逻辑，该功能可以应用于任何数据库。 EF Core 提供程序针对特定数据库的失败条件提供定制的执行策略，同时提供最佳的重试策略。

例如，SQL Server 提供程序包括专门针对 SQL Server （包括 SQL Azure）定制的执行策略。 它可以识别可重试的异常类型，并具有可用于最大重试次数、重试间隔时间等的合理默认值。

为上下文配置选项时将指定执行策略。 这通常位于派生上下文的 `OnConfiguring` 方法中：

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#OnConfiguring)]

或在 ASP.NET Core 应用程序的 `Startup.cs` 中：

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<PicnicContext>(
        options => options.UseSqlServer(
            "<connection string>",
            providerOptions => providerOptions.EnableRetryOnFailure()));
}
```

## <a name="custom-execution-strategy"></a>自定义执行策略

如果要更改任何默认值，则可以使用一种机制来注册自己的自定义执行策略。

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseMyProvider(
            "<connection string>",
            options => options.ExecutionStrategy(...));
}
```

## <a name="execution-strategies-and-transactions"></a>执行策略和事务

在出现故障时自动重试的执行策略需要能够回滚失败的重试块中的每个操作。 启用重试后，通过 EF Core 执行的每个操作都将成为其自身的可重试操作。 也就是说，如果发生暂时性故障，每个查询和对 `SaveChanges()` 的每个调用都将作为一个单元重试。

但是，如果您的代码使用来启动事务 `BeginTransaction()` 您要定义自己的需要被视为一个单元的一组操作，则需要播放该事务中的所有内容。 如果尝试在使用执行策略时执行此操作，将收到如下所示的异常：

> InvalidOperationException：配置的执行策略 "SqlServerRetryingExecutionStrategy" 不支持用户启动的事务。 使用由“DbContext.Database.CreateExecutionStrategy()”返回的执行策略执行事务（作为一个可回溯单元）中的所有操作。

解决方法是使用代表需要执行的所有内容的委托来手动调用执行策略。 如果出现暂时性失败，执行策略将再次调用委托。

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#ManualTransaction)]

此方法还可用于环境事务。

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#AmbientTransaction)]

## <a name="transaction-commit-failure-and-the-idempotency-issue"></a>事务提交失败和幂等性问题

通常，如果连接失败，当前事务将回滚。 但是，如果在提交事务时断开连接，则事务的生成状态是未知的。 

默认情况下，执行策略将重试该操作，就像事务已回滚一样，但如果不是这样，则这会导致在新数据库状态不兼容时导致异常，或者如果操作不依赖于特定状态（例如，使用自动生成的键值插入新行时），可能会导致**数据损坏**。

可以通过多种方式来处理此情况。

### <a name="option-1---do-almost-nothing"></a>选项 1-执行（几乎不）任何操作

事务提交期间连接失败的可能性很低，因此如果实际发生此情况，应用程序可能会失败。

但是，需要避免使用存储生成的密钥，以确保引发异常而不是添加重复的行。 请考虑使用客户端生成的 GUID 值或客户端值生成器。

### <a name="option-2---rebuild-application-state"></a>选项 2-重建应用程序状态

1. 放弃当前 `DbContext`。
2. 创建新 `DbContext`，并从数据库中还原应用程序的状态。
3. 通知用户上次操作可能尚未成功完成。

### <a name="option-3---add-state-verification"></a>选项 3-添加状态验证

对于大多数更改数据库状态的操作，可以添加代码来检查它是否成功。 EF 提供扩展方法，使其更易于 `IExecutionStrategy.ExecuteInTransaction`。

此方法将启动并提交事务，并且还接受 `verifySucceeded` 参数中的一个函数，该参数会在事务提交期间发生暂时性错误时调用。

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Verification)]

> [!NOTE]
> 此处 `SaveChanges` 在 `acceptAllChangesOnSuccess` 设置为 `false` 时调用，以避免 `Unchanged` 成功时将 `Blog` 实体的状态更改为 `SaveChanges`。 如果提交失败并且事务已回滚，则允许重试相同的操作。

### <a name="option-4---manually-track-the-transaction"></a>选项 4-手动跟踪事务

如果需要使用存储生成的密钥或需要一种处理提交失败且不依赖于所执行操作的通用方法，则可以为每个事务分配一个可在提交失败时进行检查的 ID。

1. 向数据库添加一个用于跟踪事务状态的表。
2. 在每个事务开头的表中插入一行。
3. 如果在提交期间连接失败，请检查数据库中是否存在相应的行。
4. 如果提交成功，则删除相应的行以避免表增长。

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Tracking)]

> [!NOTE]
> 请确保用于验证的上下文具有定义的执行策略，因为如果在事务提交期间连接失败，则连接可能在验证期间再次失败。
