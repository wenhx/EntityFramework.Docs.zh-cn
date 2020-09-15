---
title: 使用事务-EF6
description: 使用实体框架6中的事务
author: divega
ms.date: 10/23/2016
uid: ef6/saving/transactions
ms.openlocfilehash: 15cc2171338defe482767114c58afe16cc1ffe21
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073712"
---
# <a name="working-with-transactions"></a><span data-ttu-id="cc2c7-103">使用事务</span><span class="sxs-lookup"><span data-stu-id="cc2c7-103">Working with Transactions</span></span>
> [!NOTE]
> <span data-ttu-id="cc2c7-104">**仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="cc2c7-105">如果使用的是早期版本，则部分或全部信息不适用。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="cc2c7-106">本文档介绍如何在 EF6 中使用事务，包括自 EF5 后添加的增强功能，以便轻松处理事务。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-106">This document will describe using transactions in EF6 including the enhancements we have added since EF5 to make working with transactions easy.</span></span>  

## <a name="what-ef-does-by-default"></a><span data-ttu-id="cc2c7-107">默认情况下，什么是 EF</span><span class="sxs-lookup"><span data-stu-id="cc2c7-107">What EF does by default</span></span>  

<span data-ttu-id="cc2c7-108">在所有版本的实体框架中，每当你执行 \*\*SaveChanges ( # B1 \*\* 以便在数据库上执行插入、更新或删除操作时，框架会将该操作包装在事务中。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-108">In all versions of Entity Framework, whenever you execute **SaveChanges()** to insert, update or delete on the database the framework will wrap that operation in a transaction.</span></span> <span data-ttu-id="cc2c7-109">此事务只持续足够长的时间来执行操作，然后完成。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-109">This transaction lasts only long enough to execute the operation and then completes.</span></span> <span data-ttu-id="cc2c7-110">执行另一个这样的操作时，将启动新事务。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-110">When you execute another such operation a new transaction is started.</span></span>  

<span data-ttu-id="cc2c7-111">默认情况下，从 EF6 \*\*Database.ExecuteSqlCommand ( # B1 \*\* 默认情况下会在事务中包装命令（如果尚未存在）。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-111">Starting with EF6 **Database.ExecuteSqlCommand()** by default will wrap the command in a transaction if one was not already present.</span></span> <span data-ttu-id="cc2c7-112">此方法有一些重载，允许你根据需要重写此行为。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-112">There are overloads of this method that allow you to override this behavior if you wish.</span></span> <span data-ttu-id="cc2c7-113">此外，在 EF6 中通过 Api 包括在模型中的存储过程（如 \*\*ObjectContext.ExecuteFunction ( # B1 \*\* ）执行相同 (，只不过默认行为不能在) 重写。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-113">Also in EF6 execution of stored procedures included in the model through APIs such as **ObjectContext.ExecuteFunction()** does the same (except that the default behavior cannot at the moment be overridden).</span></span>  

<span data-ttu-id="cc2c7-114">在任一情况下，事务的隔离级别都是数据库提供程序认为其默认设置的任何隔离级别。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-114">In either case, the isolation level of the transaction is whatever isolation level the database provider considers its default setting.</span></span> <span data-ttu-id="cc2c7-115">例如，默认情况下，在 SQL Server 此为 "已提交读"。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-115">By default, for instance, on SQL Server this is READ COMMITTED.</span></span>  

<span data-ttu-id="cc2c7-116">实体框架不会在事务中包装查询。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-116">Entity Framework does not wrap queries in a transaction.</span></span>  

<span data-ttu-id="cc2c7-117">此默认功能适用于很多用户，如果没有，则无需在 EF6 中执行任何其他操作;只需像往常一样编写代码。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-117">This default functionality is suitable for a lot of users and if so there is no need to do anything different in EF6; just write the code as you always did.</span></span>  

<span data-ttu-id="cc2c7-118">但是，某些用户需要更好地控制其事务–以下各节将对此进行介绍。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-118">However some users require greater control over their transactions – this is covered in the following sections.</span></span>  

## <a name="how-the-apis-work"></a><span data-ttu-id="cc2c7-119">Api 的工作原理</span><span class="sxs-lookup"><span data-stu-id="cc2c7-119">How the APIs work</span></span>  

<span data-ttu-id="cc2c7-120">在实体框架 EF6 之前，在打开数据库连接自身之前 (会引发异常，如果传递的连接已) 打开。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-120">Prior to EF6 Entity Framework insisted on opening the database connection itself (it threw an exception if it was passed a connection that was already open).</span></span> <span data-ttu-id="cc2c7-121">由于只能在打开的连接上启动事务，这意味着用户可以将多个操作包装到一个事务中的唯一方法是使用[TransactionScope](https://msdn.microsoft.com/library/system.transactions.transactionscope.aspx)或使用**ObjectContext**属性，并开始直接对返回的**EntityConnection**对象调用**open ( # B1**和**BeginTransaction ( # B3** 。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-121">Since a transaction can only be started on an open connection, this meant that the only way a user could wrap several operations into one transaction was either to use a [TransactionScope](https://msdn.microsoft.com/library/system.transactions.transactionscope.aspx) or use the **ObjectContext.Connection** property and start calling **Open()** and **BeginTransaction()** directly on the returned **EntityConnection** object.</span></span> <span data-ttu-id="cc2c7-122">此外，如果你在基础数据库连接上自行启动了事务，则与数据库联系的 API 调用将失败。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-122">In addition, API calls which contacted the database would fail if you had started a transaction on the underlying database connection on your own.</span></span>  

> [!NOTE]
> <span data-ttu-id="cc2c7-123">实体框架6中删除了仅接受关闭的连接的限制。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-123">The limitation of only accepting closed connections was removed in Entity Framework 6.</span></span> <span data-ttu-id="cc2c7-124">有关详细信息，请参阅 [连接管理](xref:ef6/fundamentals/connection-management)。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-124">For details, see [Connection Management](xref:ef6/fundamentals/connection-management).</span></span>  

<span data-ttu-id="cc2c7-125">从 EF6 开始，框架现在提供：</span><span class="sxs-lookup"><span data-stu-id="cc2c7-125">Starting with EF6 the framework now provides:</span></span>  

1. <span data-ttu-id="cc2c7-126">\*\*BeginTransaction ( # B1 \*\* ：用户可以更轻松地在现有 DbContext 中启动和完成事务，并允许多个操作合并到同一个事务中，并因此全部提交或全部回滚。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-126">**Database.BeginTransaction()** : An easier method for a user to start and complete transactions themselves within an existing DbContext – allowing several operations to be combined within the same transaction and hence either all committed or all rolled back as one.</span></span> <span data-ttu-id="cc2c7-127">它还允许用户更轻松地指定事务的隔离级别。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-127">It also allows the user to more easily specify the isolation level for the transaction.</span></span>  
2. <span data-ttu-id="cc2c7-128">\*\*UseTransaction ( # B1 \*\* ：这允许 DbContext 使用在实体框架之外启动的事务。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-128">**Database.UseTransaction()** : which allows the DbContext to use a transaction which was started outside of the Entity Framework.</span></span>  

### <a name="combining-several-operations-into-one-transaction-within-the-same-context"></a><span data-ttu-id="cc2c7-129">将多个操作合并为同一上下文中的一个事务</span><span class="sxs-lookup"><span data-stu-id="cc2c7-129">Combining several operations into one transaction within the same context</span></span>  

<span data-ttu-id="cc2c7-130">\*\*BeginTransaction ( # B1 \*\* 具有两个替代，其中一个将使用显式 [IsolationLevel](https://msdn.microsoft.com/library/system.data.isolationlevel.aspx) ，另一个不采用任何参数，并从基础数据库提供程序使用默认的 IsolationLevel。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-130">**Database.BeginTransaction()** has two overrides – one which takes an explicit [IsolationLevel](https://msdn.microsoft.com/library/system.data.isolationlevel.aspx) and one which takes no arguments and uses the default IsolationLevel from the underlying database provider.</span></span> <span data-ttu-id="cc2c7-131">这两个替代都返回一个 **处理** 对象，该对象提供 \*\* ( # B1 \*\* 和 \*\*rollback ( # B3 \*\* 方法，在基础存储事务上执行提交和回滚。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-131">Both overrides return a **DbContextTransaction** object which provides **Commit()** and **Rollback()** methods which perform commit and rollback on the underlying store transaction.</span></span>  

<span data-ttu-id="cc2c7-132">**处理**在提交或回滚后会被释放。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-132">The **DbContextTransaction** is meant to be disposed once it has been committed or rolled back.</span></span> <span data-ttu-id="cc2c7-133">实现此目的的一种简单方法是 **使用 ( ... ) {...}**</span><span class="sxs-lookup"><span data-stu-id="cc2c7-133">One easy way to accomplish this is the **using(…) {…}**</span></span> <span data-ttu-id="cc2c7-134">当 using 块完成时，将自动调用 \*\*Dispose ( # B1 \*\* 的语法：</span><span class="sxs-lookup"><span data-stu-id="cc2c7-134">syntax which will automatically call **Dispose()** when the using block completes:</span></span>  

``` csharp
using System;
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
using System.Transactions;

namespace TransactionsExamples
{
    class TransactionsExample
    {
        static void StartOwnTransactionWithinContext()
        {
            using (var context = new BloggingContext())
            {
                using (var dbContextTransaction = context.Database.BeginTransaction())
                {
                    context.Database.ExecuteSqlCommand(
                        @"UPDATE Blogs SET Rating = 5" +
                            " WHERE Name LIKE '%Entity Framework%'"
                        );

                    var query = context.Posts.Where(p => p.Blog.Rating >= 5);
                    foreach (var post in query)
                    {
                        post.Title += "[Cool Blog]";
                    }

                    context.SaveChanges();

                    dbContextTransaction.Commit();
                }
            }
        }
    }
}
```  

> [!NOTE]
> <span data-ttu-id="cc2c7-135">开始事务要求基础存储连接处于打开状态。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-135">Beginning a transaction requires that the underlying store connection is open.</span></span> <span data-ttu-id="cc2c7-136">因此调用 BeginTransaction ( # A1 将打开连接（如果尚未打开）。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-136">So calling Database.BeginTransaction() will open the connection  if it is not already opened.</span></span> <span data-ttu-id="cc2c7-137">如果处理打开了连接，则在调用 Dispose ( # A1 时将关闭该连接。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-137">If DbContextTransaction opened the connection then it will close it when Dispose() is called.</span></span>  

### <a name="passing-an-existing-transaction-to-the-context"></a><span data-ttu-id="cc2c7-138">将现有事务传递到上下文</span><span class="sxs-lookup"><span data-stu-id="cc2c7-138">Passing an existing transaction to the context</span></span>  

<span data-ttu-id="cc2c7-139">有时，您想要在范围内更广泛的事务，其中包括对同一数据库的操作，而不是在 EF 的外部。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-139">Sometimes you would like a transaction which is even broader in scope and which includes operations on the same database but outside of EF completely.</span></span> <span data-ttu-id="cc2c7-140">若要实现此目的，您必须打开连接并自行启动事务，然后告诉 EF a) 使用已打开的数据库连接，而 b) 在该连接上使用现有事务。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-140">To accomplish this you must open the connection and start the transaction yourself and then tell EF a) to use the already-opened database connection, and b) to use the existing transaction on that connection.</span></span>  

<span data-ttu-id="cc2c7-141">若要执行此操作，必须在上下文类中定义和使用一个构造函数，该构造函数继承自 DbContext 构造函数，该构造函数将使用我) 现有的连接参数，并) contextOwnsConnection 的布尔值。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-141">To do this you must define and use a constructor on your context class which inherits from one of the DbContext constructors which take i) an existing connection parameter and ii) the contextOwnsConnection boolean.</span></span>  

> [!NOTE]
> <span data-ttu-id="cc2c7-142">在这种情况下，contextOwnsConnection 标志必须设置为 false。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-142">The contextOwnsConnection flag must be set to false when called in this scenario.</span></span> <span data-ttu-id="cc2c7-143">这一点很重要，因为它会通知实体框架它不应在完成后关闭连接 (例如，请参阅下面的第4行) ：</span><span class="sxs-lookup"><span data-stu-id="cc2c7-143">This is important as it informs Entity Framework that it should not close the connection when it is done with it (for example, see line 4 below):</span></span>  

``` csharp
using (var conn = new SqlConnection("..."))
{
    conn.Open();
    using (var context = new BloggingContext(conn, contextOwnsConnection: false))
    {
    }
}
```  

<span data-ttu-id="cc2c7-144">此外，如果想要避免默认设置) 并让实体框架知道已在连接上启动了现有事务，则必须自行启动该事务 (包括 IsolationLevel (参阅) 下面第33行。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-144">Furthermore, you must start the transaction yourself (including the IsolationLevel if you want to avoid the default setting) and let Entity Framework know that there is an existing transaction already started on the connection (see line 33 below).</span></span>  

<span data-ttu-id="cc2c7-145">然后，可以随意直接在 SqlConnection 本身或 DbContext 上执行数据库操作。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-145">Then you are free to execute database operations either directly on the SqlConnection itself, or on the DbContext.</span></span> <span data-ttu-id="cc2c7-146">所有此类操作在一个事务内执行。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-146">All such operations are executed within one transaction.</span></span> <span data-ttu-id="cc2c7-147">你需要负责提交或回滚事务，并为其调用 Dispose ( # A1，还负责关闭并释放数据库连接。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-147">You take responsibility for committing or rolling back the transaction and for calling Dispose() on it, as well as for closing and disposing the database connection.</span></span> <span data-ttu-id="cc2c7-148">例如：</span><span class="sxs-lookup"><span data-stu-id="cc2c7-148">For example:</span></span>  

``` csharp
using System;
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
using System.Transactions;

namespace TransactionsExamples
{
     class TransactionsExample
     {
        static void UsingExternalTransaction()
        {
            using (var conn = new SqlConnection("..."))
            {
               conn.Open();

               using (var sqlTxn = conn.BeginTransaction(System.Data.IsolationLevel.Snapshot))
               {
                   var sqlCommand = new SqlCommand();
                   sqlCommand.Connection = conn;
                   sqlCommand.Transaction = sqlTxn;
                   sqlCommand.CommandText =
                       @"UPDATE Blogs SET Rating = 5" +
                        " WHERE Name LIKE '%Entity Framework%'";
                   sqlCommand.ExecuteNonQuery();

                   using (var context =  
                     new BloggingContext(conn, contextOwnsConnection: false))
                    {
                        context.Database.UseTransaction(sqlTxn);

                        var query =  context.Posts.Where(p => p.Blog.Rating >= 5);
                        foreach (var post in query)
                        {
                            post.Title += "[Cool Blog]";
                        }
                       context.SaveChanges();
                    }

                    sqlTxn.Commit();
                }
            }
        }
    }
}
```  

### <a name="clearing-up-the-transaction"></a><span data-ttu-id="cc2c7-149">清除事务</span><span class="sxs-lookup"><span data-stu-id="cc2c7-149">Clearing up the transaction</span></span>

<span data-ttu-id="cc2c7-150">可以将 null 传递到 UseTransaction ( # A1，以清除实体框架对当前事务的了解。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-150">You can pass null to Database.UseTransaction() to clear Entity Framework’s knowledge of the current transaction.</span></span> <span data-ttu-id="cc2c7-151">当你执行此操作时，实体框架不会提交或回滚现有事务，因此请谨慎使用，仅在你确定要执行的操作时使用。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-151">Entity Framework will neither commit nor rollback the existing transaction when you do this, so use with care and only if you’re sure this is what you want to do.</span></span>  

### <a name="errors-in-usetransaction"></a><span data-ttu-id="cc2c7-152">UseTransaction 中的错误</span><span class="sxs-lookup"><span data-stu-id="cc2c7-152">Errors in UseTransaction</span></span>

<span data-ttu-id="cc2c7-153">如果在以下情况传递事务，则会在 UseTransaction ( # A1 中看到异常：</span><span class="sxs-lookup"><span data-stu-id="cc2c7-153">You will see an exception from Database.UseTransaction() if you pass a transaction when:</span></span>  
- <span data-ttu-id="cc2c7-154">实体框架已有一个现有的事务</span><span class="sxs-lookup"><span data-stu-id="cc2c7-154">Entity Framework already has an existing transaction</span></span>  
- <span data-ttu-id="cc2c7-155">实体框架已在某一 TransactionScope 内运行</span><span class="sxs-lookup"><span data-stu-id="cc2c7-155">Entity Framework is already operating within a TransactionScope</span></span>  
- <span data-ttu-id="cc2c7-156">传递的事务中的连接对象为 null。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-156">The connection object in the transaction passed is null.</span></span> <span data-ttu-id="cc2c7-157">也就是说，事务与连接无关-通常是该事务已完成的符号</span><span class="sxs-lookup"><span data-stu-id="cc2c7-157">That is, the transaction is not associated with a connection – usually this is a sign that that transaction has already completed</span></span>  
- <span data-ttu-id="cc2c7-158">传递的事务中的连接对象与实体框架的连接不匹配。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-158">The connection object in the transaction passed does not match the Entity Framework’s connection.</span></span>  

## <a name="using-transactions-with-other-features"></a><span data-ttu-id="cc2c7-159">将事务与其他功能一起使用</span><span class="sxs-lookup"><span data-stu-id="cc2c7-159">Using transactions with other features</span></span>  

<span data-ttu-id="cc2c7-160">本部分详细说明了上述事务与的交互方式：</span><span class="sxs-lookup"><span data-stu-id="cc2c7-160">This section details how the above transactions interact with:</span></span>  

- <span data-ttu-id="cc2c7-161">连接复原</span><span class="sxs-lookup"><span data-stu-id="cc2c7-161">Connection resiliency</span></span>  
- <span data-ttu-id="cc2c7-162">异步方法</span><span class="sxs-lookup"><span data-stu-id="cc2c7-162">Asynchronous methods</span></span>  
- <span data-ttu-id="cc2c7-163">TransactionScope 交易</span><span class="sxs-lookup"><span data-stu-id="cc2c7-163">TransactionScope transactions</span></span>  

### <a name="connection-resiliency"></a><span data-ttu-id="cc2c7-164">连接复原</span><span class="sxs-lookup"><span data-stu-id="cc2c7-164">Connection Resiliency</span></span>  

<span data-ttu-id="cc2c7-165">新的连接复原功能不适用于用户启动的事务。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-165">The new Connection Resiliency feature does not work with user-initiated transactions.</span></span> <span data-ttu-id="cc2c7-166">有关详细信息，请参阅 [重试执行策略](xref:ef6/fundamentals/connection-resiliency/retry-logic#user-initiated-transactions-are-not-supported)。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-166">For details, see [Retrying Execution Strategies](xref:ef6/fundamentals/connection-resiliency/retry-logic#user-initiated-transactions-are-not-supported).</span></span>  

### <a name="asynchronous-programming"></a><span data-ttu-id="cc2c7-167">异步编程</span><span class="sxs-lookup"><span data-stu-id="cc2c7-167">Asynchronous Programming</span></span>  

<span data-ttu-id="cc2c7-168">前面几节中所述的方法不需要更多选项或设置即可使用 [异步查询和保存方法](xref:ef6/fundamentals/async
)。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-168">The approach outlined in the previous sections needs no further options or settings to work with the [asynchronous query and save methods](xref:ef6/fundamentals/async
).</span></span> <span data-ttu-id="cc2c7-169">但请注意，根据您在异步方法中所执行的操作，这可能会导致长时间运行的事务，进而导致死锁或阻塞，导致整个应用程序的性能不佳。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-169">But be aware that, depending on what you do within the asynchronous methods, this may result in long-running transactions – which can in turn cause deadlocks or blocking which is bad for the performance of the overall application.</span></span>  

### <a name="transactionscope-transactions"></a><span data-ttu-id="cc2c7-170">TransactionScope 交易</span><span class="sxs-lookup"><span data-stu-id="cc2c7-170">TransactionScope Transactions</span></span>  

<span data-ttu-id="cc2c7-171">在 EF6 之前，提供更大范围事务的建议方法是使用 TransactionScope 对象：</span><span class="sxs-lookup"><span data-stu-id="cc2c7-171">Prior to EF6 the recommended way of providing larger scope transactions was to use a TransactionScope object:</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
using System.Transactions;

namespace TransactionsExamples
{
    class TransactionsExample
    {
        static void UsingTransactionScope()
        {
            using (var scope = new TransactionScope(TransactionScopeOption.Required))
            {
                using (var conn = new SqlConnection("..."))
                {
                    conn.Open();

                    var sqlCommand = new SqlCommand();
                    sqlCommand.Connection = conn;
                    sqlCommand.CommandText =
                        @"UPDATE Blogs SET Rating = 5" +
                            " WHERE Name LIKE '%Entity Framework%'";
                    sqlCommand.ExecuteNonQuery();

                    using (var context =
                        new BloggingContext(conn, contextOwnsConnection: false))
                    {
                        var query = context.Posts.Where(p => p.Blog.Rating > 5);
                        foreach (var post in query)
                        {
                            post.Title += "[Cool Blog]";
                        }
                        context.SaveChanges();
                    }
                }

                scope.Complete();
            }
        }
    }
}
```  

<span data-ttu-id="cc2c7-172">SqlConnection 和实体框架都使用环境 TransactionScope 事务，因此一起提交。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-172">The SqlConnection and Entity Framework would both use the ambient TransactionScope transaction and hence be committed together.</span></span>  

<span data-ttu-id="cc2c7-173">从 .NET 4.5.1 TransactionScope 开始，还更新为通过使用 [TransactionScopeAsyncFlowOption](https://msdn.microsoft.com/library/system.transactions.transactionscopeasyncflowoption.aspx) 枚举来处理异步方法：</span><span class="sxs-lookup"><span data-stu-id="cc2c7-173">Starting with .NET 4.5.1 TransactionScope has been updated to also work with asynchronous methods via the use of the [TransactionScopeAsyncFlowOption](https://msdn.microsoft.com/library/system.transactions.transactionscopeasyncflowoption.aspx) enumeration:</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
using System.Transactions;

namespace TransactionsExamples
{
    class TransactionsExample
    {
        public static void AsyncTransactionScope()
        {
            using (var scope = new TransactionScope(TransactionScopeAsyncFlowOption.Enabled))
            {
                using (var conn = new SqlConnection("..."))
                {
                    await conn.OpenAsync();

                    var sqlCommand = new SqlCommand();
                    sqlCommand.Connection = conn;
                    sqlCommand.CommandText =
                        @"UPDATE Blogs SET Rating = 5" +
                            " WHERE Name LIKE '%Entity Framework%'";
                    await sqlCommand.ExecuteNonQueryAsync();

                    using (var context = new BloggingContext(conn, contextOwnsConnection: false))
                    {
                        var query = context.Posts.Where(p => p.Blog.Rating > 5);
                        foreach (var post in query)
                        {
                            post.Title += "[Cool Blog]";
                        }

                        await context.SaveChangesAsync();
                    }
                }
            }
        }
    }
}
```  

<span data-ttu-id="cc2c7-174">对于 TransactionScope 方法仍有一些限制：</span><span class="sxs-lookup"><span data-stu-id="cc2c7-174">There are still some limitations to the TransactionScope approach:</span></span>  

- <span data-ttu-id="cc2c7-175">需要 .NET 4.5.1 或更高版本才能使用异步方法。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-175">Requires .NET 4.5.1 or greater to work with asynchronous methods.</span></span>  
- <span data-ttu-id="cc2c7-176">除非你确定有且只有一个连接 (云方案不支持分布式事务) ，否则不能用于云方案。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-176">It cannot be used in cloud scenarios unless you are sure you have one and only one connection (cloud scenarios do not support distributed transactions).</span></span>  
- <span data-ttu-id="cc2c7-177">它不能与 UseTransaction ( # A1 方法结合前面的部分。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-177">It cannot be combined with the Database.UseTransaction() approach of the previous sections.</span></span>  
- <span data-ttu-id="cc2c7-178">如果你颁发了任何 DDL 并且尚未通过 MSDTC 服务启用分布式事务，则它会引发异常。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-178">It will throw exceptions if you issue any DDL and have not enabled distributed transactions through the MSDTC Service.</span></span>  

<span data-ttu-id="cc2c7-179">TransactionScope 方法的优点：</span><span class="sxs-lookup"><span data-stu-id="cc2c7-179">Advantages of the TransactionScope approach:</span></span>  

- <span data-ttu-id="cc2c7-180">如果与同一事务中的另一个数据库建立了多个连接，则它会自动将本地事务升级到分布式事务，或将与一个数据库的连接组合到同一个事务中的不同数据库 (注意：您必须将 MSDTC 服务配置为允许此工作) 的分布式事务。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-180">It will automatically upgrade a local transaction to a distributed transaction if you make more than one connection to a given database or combine a connection to one database with a connection to a different database within the same transaction (note: you must have the MSDTC service configured to allow distributed transactions for this to work).</span></span>  
- <span data-ttu-id="cc2c7-181">易于编码。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-181">Ease of coding.</span></span> <span data-ttu-id="cc2c7-182">如果你更愿意在后台隐式处理事务，而不是显式地在控制下进行处理，则 TransactionScope 方法可能更适合你。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-182">If you prefer the transaction to be ambient and dealt with implicitly in the background rather than explicitly under you control then the TransactionScope approach may suit you better.</span></span>  

<span data-ttu-id="cc2c7-183">总之，在上面的 BeginTransaction ( # A1 和 UseTransaction ( # A3 Api 中，大多数用户不再需要 TransactionScope 方法。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-183">In summary, with the new Database.BeginTransaction() and Database.UseTransaction() APIs above, the TransactionScope approach is no longer necessary for most users.</span></span> <span data-ttu-id="cc2c7-184">如果继续使用 TransactionScope，请注意上述限制。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-184">If you do continue to use TransactionScope then be aware of the above limitations.</span></span> <span data-ttu-id="cc2c7-185">建议尽可能使用前面部分中所述的方法。</span><span class="sxs-lookup"><span data-stu-id="cc2c7-185">We recommend using the approach outlined in the previous sections instead where possible.</span></span>  
