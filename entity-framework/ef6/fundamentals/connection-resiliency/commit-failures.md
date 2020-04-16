---
title: 处理事务提交失败 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 5b1f7a7d-1b24-4645-95ec-5608a31ef577
ms.openlocfilehash: cf2722496e207a8ecaa9cfaa4ca61e7248e5e58f
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434131"
---
# <a name="handling-transaction-commit-failures"></a><span data-ttu-id="475f6-102">处理事务提交失败</span><span class="sxs-lookup"><span data-stu-id="475f6-102">Handling transaction commit failures</span></span>
> [!NOTE]
> <span data-ttu-id="475f6-103">**仅 EF6.1 继续**- 本页中讨论的功能、API 等在实体框架 6.1 中介绍。</span><span class="sxs-lookup"><span data-stu-id="475f6-103">**EF6.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="475f6-104">如果使用的是早期版本，则部分或全部信息不适用。</span><span class="sxs-lookup"><span data-stu-id="475f6-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="475f6-105">作为 6.1 的一部分，我们引入了 EF 的新连接恢复功能：当瞬态连接故障影响事务提交确认时自动检测和恢复的能力。</span><span class="sxs-lookup"><span data-stu-id="475f6-105">As part of 6.1 we are introducing a new connection resiliency feature for EF: the ability to detect and recover automatically when transient connection failures affect the acknowledgement of transaction commits.</span></span> <span data-ttu-id="475f6-106">场景的完整细节最好在博客文章[SQL 数据库连接和阳萎问题](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue)中描述。</span><span class="sxs-lookup"><span data-stu-id="475f6-106">The full details of the scenario are best described in the blog post [SQL Database Connectivity and the Idempotency Issue](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue).</span></span>  <span data-ttu-id="475f6-107">总之，当在事务提交期间引发异常时，有两个可能的原因：</span><span class="sxs-lookup"><span data-stu-id="475f6-107">In summary, the scenario is that when an exception is raised during a transaction commit there are two possible causes:</span></span>  

1. <span data-ttu-id="475f6-108">服务器上的事务提交失败</span><span class="sxs-lookup"><span data-stu-id="475f6-108">The transaction commit failed on the server</span></span>
2. <span data-ttu-id="475f6-109">事务提交在服务器上成功，但连接问题阻止成功通知到达客户端</span><span class="sxs-lookup"><span data-stu-id="475f6-109">The transaction commit succeeded on the server but a connectivity issue prevented the success notification from reaching the client</span></span>  

<span data-ttu-id="475f6-110">当第一种情况发生时，应用程序或用户可以重试操作，但当第二种情况发生时，应避免重试，并且应用程序可以自动恢复。</span><span class="sxs-lookup"><span data-stu-id="475f6-110">When the first situation happens the application or the user can retry the operation, but when the second situation occurs retries should be avoided and the application could recover automatically.</span></span> <span data-ttu-id="475f6-111">挑战在于，如果没有能力检测在提交过程中报告异常的实际原因，应用程序无法选择正确的操作方案。</span><span class="sxs-lookup"><span data-stu-id="475f6-111">The challenge is that without the ability to detect what was the actual reason an exception was reported during commit, the application cannot choose the right course of action.</span></span> <span data-ttu-id="475f6-112">EF 6.1 中的新功能允许 EF 在事务成功时与数据库进行仔细检查，并透明地执行正确的操作过程。</span><span class="sxs-lookup"><span data-stu-id="475f6-112">The new feature in EF 6.1 allows EF to double-check with the database if the transaction succeeded and take the right course of action transparently.</span></span>  

## <a name="using-the-feature"></a><span data-ttu-id="475f6-113">使用功能</span><span class="sxs-lookup"><span data-stu-id="475f6-113">Using the feature</span></span>  

<span data-ttu-id="475f6-114">为了启用该功能，您需要在**Db 配置**的构造函数中调用[SetTransactionHandler。](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx)</span><span class="sxs-lookup"><span data-stu-id="475f6-114">In order to enable the feature you need include a call to [SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx) in the constructor of your **DbConfiguration**.</span></span> <span data-ttu-id="475f6-115">如果您不熟悉 Db**配置**，请参阅[基于代码的配置](~/ef6/fundamentals/configuring/code-based.md)。</span><span class="sxs-lookup"><span data-stu-id="475f6-115">If you are unfamiliar with **DbConfiguration**, see [Code Based Configuration](~/ef6/fundamentals/configuring/code-based.md).</span></span> <span data-ttu-id="475f6-116">此功能可与我们在 EF6 中引入的自动重试结合使用，这有助于在事务因暂时性故障而实际无法在服务器上提交的情况：</span><span class="sxs-lookup"><span data-stu-id="475f6-116">This feature can be used in combination with the automatic retries we introduced in EF6, which help in the situation in which the transaction actually failed to commit on the server due to a transient failure:</span></span>  

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

## <a name="how-transactions-are-tracked"></a><span data-ttu-id="475f6-117">如何跟踪交易记录</span><span class="sxs-lookup"><span data-stu-id="475f6-117">How transactions are tracked</span></span>  

<span data-ttu-id="475f6-118">启用此功能后，EF 将自动向名为 **__Transactions**的数据库添加新表。</span><span class="sxs-lookup"><span data-stu-id="475f6-118">When the feature is enabled, EF will automatically add a new table to the database called **__Transactions**.</span></span> <span data-ttu-id="475f6-119">每次 EF 创建事务时，都会在此表中插入一个新行，如果提交期间发生事务失败，则检查该行是否存在。</span><span class="sxs-lookup"><span data-stu-id="475f6-119">A new row is inserted in this table every time a transaction is created by EF and that row is checked for existence if a transaction failure occurs during commit.</span></span>  

<span data-ttu-id="475f6-120">尽管 EF 将尽力在不再需要表时修剪行，但如果应用程序过早退出，则该表可能会增长，因此在某些情况下您可能需要手动清除表。</span><span class="sxs-lookup"><span data-stu-id="475f6-120">Although EF will do a best effort to prune rows from the table when they aren’t needed anymore, the table can grow if the application exits prematurely and for that reason you may need to purge the table manually in some cases.</span></span>  

## <a name="how-to-handle-commit-failures-with-previous-versions"></a><span data-ttu-id="475f6-121">如何处理具有早期版本的提交失败</span><span class="sxs-lookup"><span data-stu-id="475f6-121">How to handle commit failures with previous Versions</span></span>

<span data-ttu-id="475f6-122">在 EF 6.1 之前，没有处理 EF 产品中的提交故障的机制。</span><span class="sxs-lookup"><span data-stu-id="475f6-122">Before EF 6.1 there was not mechanism to handle commit failures in the EF product.</span></span> <span data-ttu-id="475f6-123">有几种方法可以处理这种情况，可以应用于 EF6 的早期版本：</span><span class="sxs-lookup"><span data-stu-id="475f6-123">There are several ways to dealing with this situation that can be applied to previous versions of EF6:</span></span>  

* <span data-ttu-id="475f6-124">选项 1 - 不执行任何操作</span><span class="sxs-lookup"><span data-stu-id="475f6-124">Option 1 - Do nothing</span></span>  

  <span data-ttu-id="475f6-125">事务提交期间连接失败的可能性很低，因此，如果实际发生此情况，应用程序可能会失败。</span><span class="sxs-lookup"><span data-stu-id="475f6-125">The likelihood of a connection failure during transaction commit is low so it may be acceptable for your application to just fail if this condition actually occurs.</span></span>  

* <span data-ttu-id="475f6-126">选项 2 - 使用数据库重置状态</span><span class="sxs-lookup"><span data-stu-id="475f6-126">Option 2 - Use the database to reset state</span></span>  

  1. <span data-ttu-id="475f6-127">放弃当前数据库上下文</span><span class="sxs-lookup"><span data-stu-id="475f6-127">Discard the current DbContext</span></span>  
  2. <span data-ttu-id="475f6-128">创建新的 DbContext 并从数据库还原应用程序的状态</span><span class="sxs-lookup"><span data-stu-id="475f6-128">Create a new DbContext and restore the state of your application from the database</span></span>  
  3. <span data-ttu-id="475f6-129">通知用户上次操作可能尚未成功完成</span><span class="sxs-lookup"><span data-stu-id="475f6-129">Inform the user that the last operation might not have been completed successfully</span></span>  

* <span data-ttu-id="475f6-130">选项 3 - 手动跟踪交易</span><span class="sxs-lookup"><span data-stu-id="475f6-130">Option 3 - Manually track the transaction</span></span>  

  1. <span data-ttu-id="475f6-131">将非跟踪表添加到用于跟踪事务状态的数据库。</span><span class="sxs-lookup"><span data-stu-id="475f6-131">Add a non-tracked table to the database used to track the status of the transactions.</span></span>  
  2. <span data-ttu-id="475f6-132">在每个事务开始时将一行插入到表中。</span><span class="sxs-lookup"><span data-stu-id="475f6-132">Insert a row into the table at the beginning of each transaction.</span></span>  
  3. <span data-ttu-id="475f6-133">如果在提交过程中连接失败，请检查数据库中是否存在相应的行。</span><span class="sxs-lookup"><span data-stu-id="475f6-133">If the connection fails during the commit, check for the presence of the corresponding row in the database.</span></span>  
     - <span data-ttu-id="475f6-134">如果行存在，请正常继续，因为事务已成功提交</span><span class="sxs-lookup"><span data-stu-id="475f6-134">If the row is present, continue normally, as the transaction was committed successfully</span></span>  
     - <span data-ttu-id="475f6-135">如果行不存在，请使用执行策略重试当前操作。</span><span class="sxs-lookup"><span data-stu-id="475f6-135">If the row is absent, use an execution strategy to retry the current operation.</span></span>  
  4. <span data-ttu-id="475f6-136">如果提交成功，请删除相应的行以避免表的增长。</span><span class="sxs-lookup"><span data-stu-id="475f6-136">If the commit is successful, delete the corresponding row to avoid the growth of the table.</span></span>  

<span data-ttu-id="475f6-137">[此博客文章](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue)包含用于在 SQL Azure 上完成此任务的示例代码。</span><span class="sxs-lookup"><span data-stu-id="475f6-137">[This blog post](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue) contains sample code for accomplishing this on SQL Azure.</span></span>  
