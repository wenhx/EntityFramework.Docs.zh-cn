---
title: 处理事务提交失败-EF6
description: 处理实体框架6中的事务提交失败
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/connection-resiliency/commit-failures
ms.openlocfilehash: c817e5e9342d5e0682318dc34fa3b4cdfa756228
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072845"
---
# <a name="handling-transaction-commit-failures"></a><span data-ttu-id="d06d2-103">处理事务提交失败</span><span class="sxs-lookup"><span data-stu-id="d06d2-103">Handling transaction commit failures</span></span>

> [!NOTE]
> <span data-ttu-id="d06d2-104">**Ef 6.1 仅向前** -在实体框架6.1 中引入了本页中讨论的功能、api 等。</span><span class="sxs-lookup"><span data-stu-id="d06d2-104">**EF6.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="d06d2-105">如果使用的是早期版本，则部分或全部信息不适用。</span><span class="sxs-lookup"><span data-stu-id="d06d2-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="d06d2-106">作为6.1 的一部分，我们将为 EF 引入新的连接复原功能：当暂时性连接故障影响事务提交确认时，自动检测和恢复。</span><span class="sxs-lookup"><span data-stu-id="d06d2-106">As part of 6.1 we are introducing a new connection resiliency feature for EF: the ability to detect and recover automatically when transient connection failures affect the acknowledgement of transaction commits.</span></span> <span data-ttu-id="d06d2-107">有关该方案的完整详细信息，请参阅博客文章 [SQL 数据库连接和幂等性问题](/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue)。</span><span class="sxs-lookup"><span data-stu-id="d06d2-107">The full details of the scenario are best described in the blog post [SQL Database Connectivity and the Idempotency Issue](/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue).</span></span>  <span data-ttu-id="d06d2-108">总的来说，方案是在事务提交期间引发异常时，有两个可能的原因：</span><span class="sxs-lookup"><span data-stu-id="d06d2-108">In summary, the scenario is that when an exception is raised during a transaction commit there are two possible causes:</span></span>  

1. <span data-ttu-id="d06d2-109">服务器上的事务提交失败</span><span class="sxs-lookup"><span data-stu-id="d06d2-109">The transaction commit failed on the server</span></span>
2. <span data-ttu-id="d06d2-110">服务器上的事务提交成功，但连接问题阻止成功通知到达客户端</span><span class="sxs-lookup"><span data-stu-id="d06d2-110">The transaction commit succeeded on the server but a connectivity issue prevented the success notification from reaching the client</span></span>  

<span data-ttu-id="d06d2-111">在第一种情况下，应用程序或用户可以重试该操作，但当发生第二种情况时，应避免重试，并且应用程序可能会自动恢复。</span><span class="sxs-lookup"><span data-stu-id="d06d2-111">When the first situation happens the application or the user can retry the operation, but when the second situation occurs retries should be avoided and the application could recover automatically.</span></span> <span data-ttu-id="d06d2-112">难点在于，如果不能检测到在提交期间报告异常的实际原因，应用程序将无法选择正确的操作过程。</span><span class="sxs-lookup"><span data-stu-id="d06d2-112">The challenge is that without the ability to detect what was the actual reason an exception was reported during commit, the application cannot choose the right course of action.</span></span> <span data-ttu-id="d06d2-113">EF 6.1 中的新功能允许 EF 在事务成功并以透明方式执行正确操作的过程中仔细检查数据库。</span><span class="sxs-lookup"><span data-stu-id="d06d2-113">The new feature in EF 6.1 allows EF to double-check with the database if the transaction succeeded and take the right course of action transparently.</span></span>  

## <a name="using-the-feature"></a><span data-ttu-id="d06d2-114">使用功能</span><span class="sxs-lookup"><span data-stu-id="d06d2-114">Using the feature</span></span>  

<span data-ttu-id="d06d2-115">若要启用此功能，需要在**DbConfiguration**的构造函数中包括对[SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx)的调用。</span><span class="sxs-lookup"><span data-stu-id="d06d2-115">In order to enable the feature you need include a call to [SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx) in the constructor of your **DbConfiguration**.</span></span> <span data-ttu-id="d06d2-116">如果不熟悉 **DbConfiguration**，请参阅 [基于代码的配置](xref:ef6/fundamentals/configuring/code-based)。</span><span class="sxs-lookup"><span data-stu-id="d06d2-116">If you are unfamiliar with **DbConfiguration**, see [Code Based Configuration](xref:ef6/fundamentals/configuring/code-based).</span></span> <span data-ttu-id="d06d2-117">此功能可以与我们在 EF6 中引入的自动重试结合使用，这有助于在发生暂时性故障的情况下，事务实际上未能在服务器上提交：</span><span class="sxs-lookup"><span data-stu-id="d06d2-117">This feature can be used in combination with the automatic retries we introduced in EF6, which help in the situation in which the transaction actually failed to commit on the server due to a transient failure:</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.SqlServer;

public class MyConfiguration : DbConfiguration  
{
  public MyConfiguration()  
  {  
    SetTransactionHandler(SqlProviderServices.ProviderInvariantName, () => new CommitFailureHandler());  
    SetExecutionStrategy(SqlProviderServices.ProviderInvariantName, () => new SqlAzureExecutionStrategy());  
  }  
}
```  

## <a name="how-transactions-are-tracked"></a><span data-ttu-id="d06d2-118">如何跟踪事务</span><span class="sxs-lookup"><span data-stu-id="d06d2-118">How transactions are tracked</span></span>  

<span data-ttu-id="d06d2-119">启用此功能后，EF 会自动将新表添加到名为 **__Transactions**的数据库。</span><span class="sxs-lookup"><span data-stu-id="d06d2-119">When the feature is enabled, EF will automatically add a new table to the database called **__Transactions**.</span></span> <span data-ttu-id="d06d2-120">在此表中，每次创建事务时都会在此表中插入一个新行，并在提交期间发生事务失败时检查该行是否存在。</span><span class="sxs-lookup"><span data-stu-id="d06d2-120">A new row is inserted in this table every time a transaction is created by EF and that row is checked for existence if a transaction failure occurs during commit.</span></span>  

<span data-ttu-id="d06d2-121">尽管 EF 在不再需要时可以最大程度地修剪表中的行，但如果应用程序过早退出，则表可能会增长，因此，在某些情况下，可能需要手动清除该表。</span><span class="sxs-lookup"><span data-stu-id="d06d2-121">Although EF will do a best effort to prune rows from the table when they aren’t needed anymore, the table can grow if the application exits prematurely and for that reason you may need to purge the table manually in some cases.</span></span>  

## <a name="how-to-handle-commit-failures-with-previous-versions"></a><span data-ttu-id="d06d2-122">如何处理以前版本的提交失败</span><span class="sxs-lookup"><span data-stu-id="d06d2-122">How to handle commit failures with previous Versions</span></span>

<span data-ttu-id="d06d2-123">EF 6.1 之前没有处理 EF 产品中的提交失败的机制。</span><span class="sxs-lookup"><span data-stu-id="d06d2-123">Before EF 6.1 there was not mechanism to handle commit failures in the EF product.</span></span> <span data-ttu-id="d06d2-124">可以通过多种方式来处理可应用于早期版本的 EF6 的情况：</span><span class="sxs-lookup"><span data-stu-id="d06d2-124">There are several ways to dealing with this situation that can be applied to previous versions of EF6:</span></span>  

* <span data-ttu-id="d06d2-125">选项 1-不执行任何操作</span><span class="sxs-lookup"><span data-stu-id="d06d2-125">Option 1 - Do nothing</span></span>  

  <span data-ttu-id="d06d2-126">在事务提交期间连接失败的可能性较低，因此，如果实际发生此情况，应用程序就会失败。</span><span class="sxs-lookup"><span data-stu-id="d06d2-126">The likelihood of a connection failure during transaction commit is low so it may be acceptable for your application to just fail if this condition actually occurs.</span></span>  

* <span data-ttu-id="d06d2-127">选项 2-使用数据库重置状态</span><span class="sxs-lookup"><span data-stu-id="d06d2-127">Option 2 - Use the database to reset state</span></span>  

  1. <span data-ttu-id="d06d2-128">放弃当前 DbContext</span><span class="sxs-lookup"><span data-stu-id="d06d2-128">Discard the current DbContext</span></span>  
  2. <span data-ttu-id="d06d2-129">创建新的 DbContext 并从数据库还原应用程序的状态</span><span class="sxs-lookup"><span data-stu-id="d06d2-129">Create a new DbContext and restore the state of your application from the database</span></span>  
  3. <span data-ttu-id="d06d2-130">通知用户上一次操作可能未成功完成</span><span class="sxs-lookup"><span data-stu-id="d06d2-130">Inform the user that the last operation might not have been completed successfully</span></span>  

* <span data-ttu-id="d06d2-131">选项 3-手动跟踪事务</span><span class="sxs-lookup"><span data-stu-id="d06d2-131">Option 3 - Manually track the transaction</span></span>  

  1. <span data-ttu-id="d06d2-132">将非跟踪表添加到用于跟踪事务状态的数据库。</span><span class="sxs-lookup"><span data-stu-id="d06d2-132">Add a non-tracked table to the database used to track the status of the transactions.</span></span>  
  2. <span data-ttu-id="d06d2-133">在每个事务开头的表中插入一行。</span><span class="sxs-lookup"><span data-stu-id="d06d2-133">Insert a row into the table at the beginning of each transaction.</span></span>  
  3. <span data-ttu-id="d06d2-134">如果在提交期间连接失败，请检查数据库中是否存在相应的行。</span><span class="sxs-lookup"><span data-stu-id="d06d2-134">If the connection fails during the commit, check for the presence of the corresponding row in the database.</span></span>  
     * <span data-ttu-id="d06d2-135">如果行存在，则继续正常，因为事务已成功提交</span><span class="sxs-lookup"><span data-stu-id="d06d2-135">If the row is present, continue normally, as the transaction was committed successfully</span></span>  
     * <span data-ttu-id="d06d2-136">如果该行不存在，请使用执行策略重试当前操作。</span><span class="sxs-lookup"><span data-stu-id="d06d2-136">If the row is absent, use an execution strategy to retry the current operation.</span></span>  
  4. <span data-ttu-id="d06d2-137">如果提交成功，则删除相应的行以避免表增长。</span><span class="sxs-lookup"><span data-stu-id="d06d2-137">If the commit is successful, delete the corresponding row to avoid the growth of the table.</span></span>  

<span data-ttu-id="d06d2-138">[此博客文章](/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue) 包含 SQL Azure 的示例代码。</span><span class="sxs-lookup"><span data-stu-id="d06d2-138">[This blog post](/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue) contains sample code for accomplishing this on SQL Azure.</span></span>  
