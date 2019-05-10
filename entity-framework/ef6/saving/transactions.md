---
title: 使用事务-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 0d0f1824-d781-4cb3-8fda-b7eaefced1cd
ms.openlocfilehash: 96cfff4cca59ab27dd68f50d0260e90902e33a92
ms.sourcegitcommit: eefcab31142f61a7aaeac03ea90dcd39f158b8b8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2019
ms.locfileid: "64873233"
---
# <a name="working-with-transactions"></a><span data-ttu-id="04f47-102">使用事务</span><span class="sxs-lookup"><span data-stu-id="04f47-102">Working with Transactions</span></span>
> [!NOTE]
> <span data-ttu-id="04f47-103">**仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。</span><span class="sxs-lookup"><span data-stu-id="04f47-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="04f47-104">如果使用的是早期版本，则部分或全部信息不适用。</span><span class="sxs-lookup"><span data-stu-id="04f47-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="04f47-105">本文档将介绍使用 EF6 中的事务包括我们添加了自 EF5 来简化使用事务的增强功能。</span><span class="sxs-lookup"><span data-stu-id="04f47-105">This document will describe using transactions in EF6 including the enhancements we have added since EF5 to make working with transactions easy.</span></span>  

## <a name="what-ef-does-by-default"></a><span data-ttu-id="04f47-106">EF 默认情况下的用途</span><span class="sxs-lookup"><span data-stu-id="04f47-106">What EF does by default</span></span>  

<span data-ttu-id="04f47-107">在所有版本的实体框架中，每次执行**savechanges （)** 若要插入、 更新或删除对数据库框架将包装该操作在事务中。</span><span class="sxs-lookup"><span data-stu-id="04f47-107">In all versions of Entity Framework, whenever you execute **SaveChanges()** to insert, update or delete on the database the framework will wrap that operation in a transaction.</span></span> <span data-ttu-id="04f47-108">此事务持续仅足够长的时间以执行此操作，然后完成。</span><span class="sxs-lookup"><span data-stu-id="04f47-108">This transaction lasts only long enough to execute the operation and then completes.</span></span> <span data-ttu-id="04f47-109">当您执行另一个此类操作时被启动新事务。</span><span class="sxs-lookup"><span data-stu-id="04f47-109">When you execute another such operation a new transaction is started.</span></span>  

<span data-ttu-id="04f47-110">从 EF6 **Database.ExecuteSqlCommand()** 默认情况下将自动换行命令在事务中如果尚未存在。</span><span class="sxs-lookup"><span data-stu-id="04f47-110">Starting with EF6 **Database.ExecuteSqlCommand()** by default will wrap the command in a transaction if one was not already present.</span></span> <span data-ttu-id="04f47-111">没有可用于重写此行为，如果您希望此方法的重载。</span><span class="sxs-lookup"><span data-stu-id="04f47-111">There are overloads of this method that allow you to override this behavior if you wish.</span></span> <span data-ttu-id="04f47-112">此外会在 EF6 执行如通过 Api 模型中包含的存储过程**ObjectContext.ExecuteFunction()** （不过，可以覆盖默认行为不能在时刻） 执行相同的操作。</span><span class="sxs-lookup"><span data-stu-id="04f47-112">Also in EF6 execution of stored procedures included in the model through APIs such as **ObjectContext.ExecuteFunction()** does the same (except that the default behavior cannot at the moment be overridden).</span></span>  

<span data-ttu-id="04f47-113">在任一情况下，事务的隔离级别是任何隔离级别的数据库提供程序认为其默认设置。</span><span class="sxs-lookup"><span data-stu-id="04f47-113">In either case, the isolation level of the transaction is whatever isolation level the database provider considers its default setting.</span></span> <span data-ttu-id="04f47-114">默认情况下，例如，SQL Server 上这是 READ COMMITTED。</span><span class="sxs-lookup"><span data-stu-id="04f47-114">By default, for instance, on SQL Server this is READ COMMITTED.</span></span>  

<span data-ttu-id="04f47-115">不，实体框架不会将查询包装在事务中。</span><span class="sxs-lookup"><span data-stu-id="04f47-115">Entity Framework does not wrap queries in a transaction.</span></span>  

<span data-ttu-id="04f47-116">此默认功能适合大量用户，如果因此，无需执行任何差异 EF6; 中如往常一样，只需编写的代码。</span><span class="sxs-lookup"><span data-stu-id="04f47-116">This default functionality is suitable for a lot of users and if so there is no need to do anything different in EF6; just write the code as you always did.</span></span>  

<span data-ttu-id="04f47-117">但是，一些用户需要更好地控制他们的事务 – 以下各节中对此进行了。</span><span class="sxs-lookup"><span data-stu-id="04f47-117">However some users require greater control over their transactions – this is covered in the following sections.</span></span>  

## <a name="how-the-apis-work"></a><span data-ttu-id="04f47-118">Api 工作原理</span><span class="sxs-lookup"><span data-stu-id="04f47-118">How the APIs work</span></span>  

<span data-ttu-id="04f47-119">在 EF6 实体框架之前他们坚持认为在打开数据库连接本身 （如果它已处于已打开的连接，它引发了异常）。</span><span class="sxs-lookup"><span data-stu-id="04f47-119">Prior to EF6 Entity Framework insisted on opening the database connection itself (it threw an exception if it was passed a connection that was already open).</span></span> <span data-ttu-id="04f47-120">由于只能对打开的连接启动事务，这意味着用户可以包装到一个事务的多个操作的唯一方式是使用[TransactionScope](https://msdn.microsoft.com/library/system.transactions.transactionscope.aspx)或使用**ObjectContext.Connection**属性并开始调用**open （)** 并**BeginTransaction()** 直接对返回**EntityConnection**对象。</span><span class="sxs-lookup"><span data-stu-id="04f47-120">Since a transaction can only be started on an open connection, this meant that the only way a user could wrap several operations into one transaction was either to use a [TransactionScope](https://msdn.microsoft.com/library/system.transactions.transactionscope.aspx) or use the **ObjectContext.Connection** property and start calling **Open()** and **BeginTransaction()** directly on the returned **EntityConnection** object.</span></span> <span data-ttu-id="04f47-121">此外，如果你在自己的基础数据库连接上启动事务，就会失败的联系数据库 API 调用。</span><span class="sxs-lookup"><span data-stu-id="04f47-121">In addition, API calls which contacted the database would fail if you had started a transaction on the underlying database connection on your own.</span></span>  

> [!NOTE]
> <span data-ttu-id="04f47-122">仅接受关闭的连接的限制已在 Entity Framework 6 中删除。</span><span class="sxs-lookup"><span data-stu-id="04f47-122">The limitation of only accepting closed connections was removed in Entity Framework 6.</span></span> <span data-ttu-id="04f47-123">有关详细信息，请参阅[连接管理](~/ef6/fundamentals/connection-management.md)。</span><span class="sxs-lookup"><span data-stu-id="04f47-123">For details, see [Connection Management](~/ef6/fundamentals/connection-management.md).</span></span>  

<span data-ttu-id="04f47-124">从开始 EF6 framework 现在提供：</span><span class="sxs-lookup"><span data-stu-id="04f47-124">Starting with EF6 the framework now provides:</span></span>  

1. <span data-ttu-id="04f47-125">**Database.BeginTransaction()** :若要启动并完成事务本身内现有的 DbContext – 允许多个操作来组合在同一事务中的用户更简单的方法，因此所有已提交或回滚作为一个所有。</span><span class="sxs-lookup"><span data-stu-id="04f47-125">**Database.BeginTransaction()** : An easier method for a user to start and complete transactions themselves within an existing DbContext – allowing several operations to be combined within the same transaction and hence either all committed or all rolled back as one.</span></span> <span data-ttu-id="04f47-126">它还允许用户更轻松地指定事务的隔离级别。</span><span class="sxs-lookup"><span data-stu-id="04f47-126">It also allows the user to more easily specify the isolation level for the transaction.</span></span>  
2. <span data-ttu-id="04f47-127">**Database.UseTransaction()** ： 它允许使用 Entity Framework 外部启动的事务的 DbContext。</span><span class="sxs-lookup"><span data-stu-id="04f47-127">**Database.UseTransaction()** : which allows the DbContext to use a transaction which was started outside of the Entity Framework.</span></span>  

### <a name="combining-several-operations-into-one-transaction-within-the-same-context"></a><span data-ttu-id="04f47-128">将组合到一个事务在同一上下文中的多个操作</span><span class="sxs-lookup"><span data-stu-id="04f47-128">Combining several operations into one transaction within the same context</span></span>  

<span data-ttu-id="04f47-129">**Database.BeginTransaction()** 具有两个替代 – 其中一个采用显式[IsolationLevel](https://msdn.microsoft.com/library/system.data.isolationlevel.aspx) ，另一个不采用任何参数，并使用默认隔离级别从基础数据库提供程序。</span><span class="sxs-lookup"><span data-stu-id="04f47-129">**Database.BeginTransaction()** has two overrides – one which takes an explicit [IsolationLevel](https://msdn.microsoft.com/library/system.data.isolationlevel.aspx) and one which takes no arguments and uses the default IsolationLevel from the underlying database provider.</span></span> <span data-ttu-id="04f47-130">这两个重写返回**DbContextTransaction**对象提供**commit （)** 并**rollback （)** 对基础存储区中执行 commit 和 rollback 方法事务。</span><span class="sxs-lookup"><span data-stu-id="04f47-130">Both overrides return a **DbContextTransaction** object which provides **Commit()** and **Rollback()** methods which perform commit and rollback on the underlying store transaction.</span></span>  

<span data-ttu-id="04f47-131">**DbContextTransaction**旨在被释放后提交或回滚。</span><span class="sxs-lookup"><span data-stu-id="04f47-131">The **DbContextTransaction** is meant to be disposed once it has been committed or rolled back.</span></span> <span data-ttu-id="04f47-132">一种简单方法完成此操作是**using(...){...}**</span><span class="sxs-lookup"><span data-stu-id="04f47-132">One easy way to accomplish this is the **using(…) {…}**</span></span> <span data-ttu-id="04f47-133">将自动调用的语法**dispose （)** using 代码块时完成：</span><span class="sxs-lookup"><span data-stu-id="04f47-133">syntax which will automatically call **Dispose()** when the using block completes:</span></span>  

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
                    try
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
                    catch (Exception)
                    {
                        dbContextTransaction.Rollback();
                    }
                }
            }
        }
    }
}
```  

> [!NOTE]
> <span data-ttu-id="04f47-134">开始事务需要基础存储区连接打开。</span><span class="sxs-lookup"><span data-stu-id="04f47-134">Beginning a transaction requires that the underlying store connection is open.</span></span> <span data-ttu-id="04f47-135">如果尚未打开，因此调用 Database.BeginTransaction() 将打开连接。</span><span class="sxs-lookup"><span data-stu-id="04f47-135">So calling Database.BeginTransaction() will open the connection  if it is not already opened.</span></span> <span data-ttu-id="04f47-136">如果 DbContextTransaction 打开连接然后它将关闭它调用 dispose （） 时。</span><span class="sxs-lookup"><span data-stu-id="04f47-136">If DbContextTransaction opened the connection then it will close it when Dispose() is called.</span></span>  

### <a name="passing-an-existing-transaction-to-the-context"></a><span data-ttu-id="04f47-137">将现有事务传递到上下文</span><span class="sxs-lookup"><span data-stu-id="04f47-137">Passing an existing transaction to the context</span></span>  

<span data-ttu-id="04f47-138">有时您希望即作用域中更广泛和完全包括在同一数据库，但在 EF 外部操作的事务。</span><span class="sxs-lookup"><span data-stu-id="04f47-138">Sometimes you would like a transaction which is even broader in scope and which includes operations on the same database but outside of EF completely.</span></span> <span data-ttu-id="04f47-139">若要完成此操作必须打开的连接和自行启动事务，然后告诉 EF） 若要将已打开数据库连接和 b） 到该连接上使用现有事务。</span><span class="sxs-lookup"><span data-stu-id="04f47-139">To accomplish this you must open the connection and start the transaction yourself and then tell EF a) to use the already-opened database connection, and b) to use the existing transaction on that connection.</span></span>  

<span data-ttu-id="04f47-140">若要执行此操作必须定义并在上下文类，该类继承自 DbContext 构造函数采用 i） 现有的连接参数和 ii) contextOwnsConnection 布尔之一上使用的构造函数。</span><span class="sxs-lookup"><span data-stu-id="04f47-140">To do this you must define and use a constructor on your context class which inherits from one of the DbContext constructors which take i) an existing connection parameter and ii) the contextOwnsConnection boolean.</span></span>  

> [!NOTE]
> <span data-ttu-id="04f47-141">ContextOwnsConnection 标志必须设置为 false 时调用在此方案中。</span><span class="sxs-lookup"><span data-stu-id="04f47-141">The contextOwnsConnection flag must be set to false when called in this scenario.</span></span> <span data-ttu-id="04f47-142">这是非常重要，因为它会通知 Entity Framework，它不应关闭连接时做了 （有关示例，请参阅以下 4 行）：</span><span class="sxs-lookup"><span data-stu-id="04f47-142">This is important as it informs Entity Framework that it should not close the connection when it is done with it (for example, see line 4 below):</span></span>  

``` csharp
using (var conn = new SqlConnection("..."))
{
    conn.Open();
    using (var context = new BloggingContext(conn, contextOwnsConnection: false))
    {
    }
}
```  

<span data-ttu-id="04f47-143">此外，您必须自己 （如果你想要避免的默认设置，包括 IsolationLevel） 启动事务，并允许实体框架知道已启动的连接上的现有事务 （请参阅下面的 33 行）。</span><span class="sxs-lookup"><span data-stu-id="04f47-143">Furthermore, you must start the transaction yourself (including the IsolationLevel if you want to avoid the default setting) and let Entity Framework know that there is an existing transaction already started on the connection (see line 33 below).</span></span>  

<span data-ttu-id="04f47-144">然后，你可以随意执行数据库操作，直接对 SqlConnection 本身，或在 DbContext。</span><span class="sxs-lookup"><span data-stu-id="04f47-144">Then you are free to execute database operations either directly on the SqlConnection itself, or on the DbContext.</span></span> <span data-ttu-id="04f47-145">在一个事务内执行所有此类操作。</span><span class="sxs-lookup"><span data-stu-id="04f47-145">All such operations are executed within one transaction.</span></span> <span data-ttu-id="04f47-146">你可以提交或回滚事务和调用 dispose （），以及关闭和释放数据库连接。</span><span class="sxs-lookup"><span data-stu-id="04f47-146">You take responsibility for committing or rolling back the transaction and for calling Dispose() on it, as well as for closing and disposing the database connection.</span></span> <span data-ttu-id="04f47-147">例如：</span><span class="sxs-lookup"><span data-stu-id="04f47-147">For example:</span></span>  

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
                   try
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
                    catch (Exception)
                    {
                        sqlTxn.Rollback();
                    }
                }
            }
        }
    }
}
```  

### <a name="clearing-up-the-transaction"></a><span data-ttu-id="04f47-148">清理事务</span><span class="sxs-lookup"><span data-stu-id="04f47-148">Clearing up the transaction</span></span>

<span data-ttu-id="04f47-149">您可以将 null 传递到 Database.UseTransaction() 以清除当前事务的实体框架的知识。</span><span class="sxs-lookup"><span data-stu-id="04f47-149">You can pass null to Database.UseTransaction() to clear Entity Framework’s knowledge of the current transaction.</span></span> <span data-ttu-id="04f47-150">实体框架将既不提交或回滚现有事务时执行此操作，因此请谨慎使用，并仅当您确定这是你想要执行操作。</span><span class="sxs-lookup"><span data-stu-id="04f47-150">Entity Framework will neither commit nor rollback the existing transaction when you do this, so use with care and only if you’re sure this is what you want to do.</span></span>  

### <a name="errors-in-usetransaction"></a><span data-ttu-id="04f47-151">UseTransaction 中的错误</span><span class="sxs-lookup"><span data-stu-id="04f47-151">Errors in UseTransaction</span></span>

<span data-ttu-id="04f47-152">如果传递的事务，则会看到从 Database.UseTransaction() 异常时：</span><span class="sxs-lookup"><span data-stu-id="04f47-152">You will see an exception from Database.UseTransaction() if you pass a transaction when:</span></span>  
- <span data-ttu-id="04f47-153">实体框架已具有现有事务</span><span class="sxs-lookup"><span data-stu-id="04f47-153">Entity Framework already has an existing transaction</span></span>  
- <span data-ttu-id="04f47-154">在 TransactionScope 内已运行实体框架</span><span class="sxs-lookup"><span data-stu-id="04f47-154">Entity Framework is already operating within a TransactionScope</span></span>  
- <span data-ttu-id="04f47-155">传入的事务中的连接对象为 null。</span><span class="sxs-lookup"><span data-stu-id="04f47-155">The connection object in the transaction passed is null.</span></span> <span data-ttu-id="04f47-156">也就是说，该事务不是与连接相关联 – 通常这是一个信号，该事务已完成</span><span class="sxs-lookup"><span data-stu-id="04f47-156">That is, the transaction is not associated with a connection – usually this is a sign that that transaction has already completed</span></span>  
- <span data-ttu-id="04f47-157">传入的事务中的连接对象与实体框架的连接不匹配。</span><span class="sxs-lookup"><span data-stu-id="04f47-157">The connection object in the transaction passed does not match the Entity Framework’s connection.</span></span>  

## <a name="using-transactions-with-other-features"></a><span data-ttu-id="04f47-158">与其他功能配合使用事务</span><span class="sxs-lookup"><span data-stu-id="04f47-158">Using transactions with other features</span></span>  

<span data-ttu-id="04f47-159">本部分详细介绍上述的事务与交互的方式：</span><span class="sxs-lookup"><span data-stu-id="04f47-159">This section details how the above transactions interact with:</span></span>  

- <span data-ttu-id="04f47-160">连接复原</span><span class="sxs-lookup"><span data-stu-id="04f47-160">Connection resiliency</span></span>  
- <span data-ttu-id="04f47-161">异步方法</span><span class="sxs-lookup"><span data-stu-id="04f47-161">Asynchronous methods</span></span>  
- <span data-ttu-id="04f47-162">TransactionScope 事务</span><span class="sxs-lookup"><span data-stu-id="04f47-162">TransactionScope transactions</span></span>  

### <a name="connection-resiliency"></a><span data-ttu-id="04f47-163">连接复原</span><span class="sxs-lookup"><span data-stu-id="04f47-163">Connection Resiliency</span></span>  

<span data-ttu-id="04f47-164">新的连接复原功能并不适用于用户启动事务。</span><span class="sxs-lookup"><span data-stu-id="04f47-164">The new Connection Resiliency feature does not work with user-initiated transactions.</span></span> <span data-ttu-id="04f47-165">有关详细信息，请参阅[重试执行策略](~/ef6/fundamentals/connection-resiliency/retry-logic.md#user-initiated-transactions-are-not-supported)。</span><span class="sxs-lookup"><span data-stu-id="04f47-165">For details, see [Retrying Execution Strategies](~/ef6/fundamentals/connection-resiliency/retry-logic.md#user-initiated-transactions-are-not-supported).</span></span>  

### <a name="asynchronous-programming"></a><span data-ttu-id="04f47-166">异步编程</span><span class="sxs-lookup"><span data-stu-id="04f47-166">Asynchronous Programming</span></span>  

<span data-ttu-id="04f47-167">前面几节中列出的方法需要任何进一步的选项或设置，以使用[异步查询和保存方法](~/ef6/fundamentals/async.md
)。</span><span class="sxs-lookup"><span data-stu-id="04f47-167">The approach outlined in the previous sections needs no further options or settings to work with the [asynchronous query and save methods](~/ef6/fundamentals/async.md
).</span></span> <span data-ttu-id="04f47-168">但请注意，具体取决于您做什么异步方法中，这可能会导致长时间运行的事务 – 这又可能导致死锁或阻止这错误的整个应用程序的性能。</span><span class="sxs-lookup"><span data-stu-id="04f47-168">But be aware that, depending on what you do within the asynchronous methods, this may result in long-running transactions – which can in turn cause deadlocks or blocking which is bad for the performance of the overall application.</span></span>  

### <a name="transactionscope-transactions"></a><span data-ttu-id="04f47-169">TransactionScope 事务</span><span class="sxs-lookup"><span data-stu-id="04f47-169">TransactionScope Transactions</span></span>  

<span data-ttu-id="04f47-170">在 EF6 之前提供较大范围的事务的推荐的方法是使用 TransactionScope 对象：</span><span class="sxs-lookup"><span data-stu-id="04f47-170">Prior to EF6 the recommended way of providing larger scope transactions was to use a TransactionScope object:</span></span>  

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

<span data-ttu-id="04f47-171">SqlConnection 和 Entity Framework 会同时使用环境的 TransactionScope 事务，因此是已提交到一起。</span><span class="sxs-lookup"><span data-stu-id="04f47-171">The SqlConnection and Entity Framework would both use the ambient TransactionScope transaction and hence be committed together.</span></span>  

<span data-ttu-id="04f47-172">从.NET 4.5.1 TransactionScope 已更新为也适用于通过使用异步方法[TransactionScopeAsyncFlowOption](https://msdn.microsoft.com/library/system.transactions.transactionscopeasyncflowoption.aspx)枚举：</span><span class="sxs-lookup"><span data-stu-id="04f47-172">Starting with .NET 4.5.1 TransactionScope has been updated to also work with asynchronous methods via the use of the [TransactionScopeAsyncFlowOption](https://msdn.microsoft.com/library/system.transactions.transactionscopeasyncflowoption.aspx) enumeration:</span></span>  

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

<span data-ttu-id="04f47-173">仍有一些限制 TransactionScope 方法：</span><span class="sxs-lookup"><span data-stu-id="04f47-173">There are still some limitations to the TransactionScope approach:</span></span>  

- <span data-ttu-id="04f47-174">需要.NET 4.5.1 或更高版本才能使用异步方法。</span><span class="sxs-lookup"><span data-stu-id="04f47-174">Requires .NET 4.5.1 or greater to work with asynchronous methods.</span></span>  
- <span data-ttu-id="04f47-175">它不能使用云方案除非您是确保你具有一个且只有一个连接 （云方案不支持分布式的事务）。</span><span class="sxs-lookup"><span data-stu-id="04f47-175">It cannot be used in cloud scenarios unless you are sure you have one and only one connection (cloud scenarios do not support distributed transactions).</span></span>  
- <span data-ttu-id="04f47-176">它不能结合前面部分的 Database.UseTransaction() 方法。</span><span class="sxs-lookup"><span data-stu-id="04f47-176">It cannot be combined with the Database.UseTransaction() approach of the previous sections.</span></span>  
- <span data-ttu-id="04f47-177">如果发出任何 DDL，未启用通过 MSDTC 服务的分布式的事务，它将引发异常。</span><span class="sxs-lookup"><span data-stu-id="04f47-177">It will throw exceptions if you issue any DDL and have not enabled distributed transactions through the MSDTC Service.</span></span>  

<span data-ttu-id="04f47-178">TransactionScope 方法的优点：</span><span class="sxs-lookup"><span data-stu-id="04f47-178">Advantages of the TransactionScope approach:</span></span>  

- <span data-ttu-id="04f47-179">它将自动升级本地事务为分布式事务如果建立到给定数据库的多个连接或连接到不同的数据库在同一事务中合并到一个数据库的连接 (请注意： 必须具有MSDTC 服务配置为允许为实现此目的的分布式的事务）。</span><span class="sxs-lookup"><span data-stu-id="04f47-179">It will automatically upgrade a local transaction to a distributed transaction if you make more than one connection to a given database or combine a connection to one database with a connection to a different database within the same transaction (note: you must have the MSDTC service configured to allow distributed transactions for this to work).</span></span>  
- <span data-ttu-id="04f47-180">编码难度。</span><span class="sxs-lookup"><span data-stu-id="04f47-180">Ease of coding.</span></span> <span data-ttu-id="04f47-181">如果想要环境和已发的牌与后台中隐式事务，而不是显式下控制然后 TransactionScope 方法可能比较适合更好。</span><span class="sxs-lookup"><span data-stu-id="04f47-181">If you prefer the transaction to be ambient and dealt with implicitly in the background rather than explicitly under you control then the TransactionScope approach may suit you better.</span></span>  

<span data-ttu-id="04f47-182">总之，使用新的 Database.BeginTransaction() 和 Database.UseTransaction() Api 更高版本，TransactionScope 方法不再需要对于大多数用户。</span><span class="sxs-lookup"><span data-stu-id="04f47-182">In summary, with the new Database.BeginTransaction() and Database.UseTransaction() APIs above, the TransactionScope approach is no longer necessary for most users.</span></span> <span data-ttu-id="04f47-183">如果不要继续使用 TransactionScope 然后请注意上述限制。</span><span class="sxs-lookup"><span data-stu-id="04f47-183">If you do continue to use TransactionScope then be aware of the above limitations.</span></span> <span data-ttu-id="04f47-184">我们建议使用列出的方法前面各节中改为在可能的情况。</span><span class="sxs-lookup"><span data-stu-id="04f47-184">We recommend using the approach outlined in the previous sections instead where possible.</span></span>  
