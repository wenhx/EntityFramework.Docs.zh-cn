---
title: 使用事务-EF6
description: 使用实体框架6中的事务
author: divega
ms.date: 10/23/2016
ms.assetid: 0d0f1824-d781-4cb3-8fda-b7eaefced1cd
uid: ef6/saving/transactions
ms.openlocfilehash: 65eebd82d4f9c583885af72d5b3cffd79fedf623
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619855"
---
# <a name="working-with-transactions"></a>使用事务
> [!NOTE]
> **仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。 如果使用的是早期版本，则部分或全部信息不适用。  

本文档介绍如何在 EF6 中使用事务，包括自 EF5 后添加的增强功能，以便轻松处理事务。  

## <a name="what-ef-does-by-default"></a>默认情况下，什么是 EF  

在所有版本的实体框架中，每当你执行 **SaveChanges ( # B1 ** 以便在数据库上执行插入、更新或删除操作时，框架会将该操作包装在事务中。 此事务只持续足够长的时间来执行操作，然后完成。 执行另一个这样的操作时，将启动新事务。  

默认情况下，从 EF6 **Database.ExecuteSqlCommand ( # B1 ** 默认情况下会在事务中包装命令（如果尚未存在）。 此方法有一些重载，允许你根据需要重写此行为。 此外，在 EF6 中通过 Api 包括在模型中的存储过程（如 **ObjectContext.ExecuteFunction ( # B1 ** ）执行相同 (，只不过默认行为不能在) 重写。  

在任一情况下，事务的隔离级别都是数据库提供程序认为其默认设置的任何隔离级别。 例如，默认情况下，在 SQL Server 此为 "已提交读"。  

实体框架不会在事务中包装查询。  

此默认功能适用于很多用户，如果没有，则无需在 EF6 中执行任何其他操作;只需像往常一样编写代码。  

但是，某些用户需要更好地控制其事务–以下各节将对此进行介绍。  

## <a name="how-the-apis-work"></a>Api 的工作原理  

在实体框架 EF6 之前，在打开数据库连接自身之前 (会引发异常，如果传递的连接已) 打开。 由于只能在打开的连接上启动事务，这意味着用户可以将多个操作包装到一个事务中的唯一方法是使用[TransactionScope](https://msdn.microsoft.com/library/system.transactions.transactionscope.aspx)或使用**ObjectContext**属性，并开始直接对返回的**EntityConnection**对象调用**open ( # B1**和**BeginTransaction ( # B3** 。 此外，如果你在基础数据库连接上自行启动了事务，则与数据库联系的 API 调用将失败。  

> [!NOTE]
> 实体框架6中删除了仅接受关闭的连接的限制。 有关详细信息，请参阅 [连接管理](xref:ef6/fundamentals/connection-management)。  

从 EF6 开始，框架现在提供：  

1. **BeginTransaction ( # B1 ** ：用户可以更轻松地在现有 DbContext 中启动和完成事务，并允许多个操作合并到同一个事务中，并因此全部提交或全部回滚。 它还允许用户更轻松地指定事务的隔离级别。  
2. **UseTransaction ( # B1 ** ：这允许 DbContext 使用在实体框架之外启动的事务。  

### <a name="combining-several-operations-into-one-transaction-within-the-same-context"></a>将多个操作合并为同一上下文中的一个事务  

**BeginTransaction ( # B1 ** 具有两个替代，其中一个将使用显式 [IsolationLevel](https://msdn.microsoft.com/library/system.data.isolationlevel.aspx) ，另一个不采用任何参数，并从基础数据库提供程序使用默认的 IsolationLevel。 这两个替代都返回一个 **处理** 对象，该对象提供 ** ( # B1 ** 和 **rollback ( # B3 ** 方法，在基础存储事务上执行提交和回滚。  

**处理**在提交或回滚后会被释放。 实现此目的的一种简单方法是 **使用 ( ... ) {...}** 当 using 块完成时，将自动调用 **Dispose ( # B1 ** 的语法：  

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
> 开始事务要求基础存储连接处于打开状态。 因此调用 BeginTransaction ( # A1 将打开连接（如果尚未打开）。 如果处理打开了连接，则在调用 Dispose ( # A1 时将关闭该连接。  

### <a name="passing-an-existing-transaction-to-the-context"></a>将现有事务传递到上下文  

有时，您想要在范围内更广泛的事务，其中包括对同一数据库的操作，而不是在 EF 的外部。 若要实现此目的，您必须打开连接并自行启动事务，然后告诉 EF a) 使用已打开的数据库连接，而 b) 在该连接上使用现有事务。  

若要执行此操作，必须在上下文类中定义和使用一个构造函数，该构造函数继承自 DbContext 构造函数，该构造函数将使用我) 现有的连接参数，并) contextOwnsConnection 的布尔值。  

> [!NOTE]
> 在这种情况下，contextOwnsConnection 标志必须设置为 false。 这一点很重要，因为它会通知实体框架它不应在完成后关闭连接 (例如，请参阅下面的第4行) ：  

``` csharp
using (var conn = new SqlConnection("..."))
{
    conn.Open();
    using (var context = new BloggingContext(conn, contextOwnsConnection: false))
    {
    }
}
```  

此外，如果想要避免默认设置) 并让实体框架知道已在连接上启动了现有事务，则必须自行启动该事务 (包括 IsolationLevel (参阅) 下面第33行。  

然后，可以随意直接在 SqlConnection 本身或 DbContext 上执行数据库操作。 所有此类操作在一个事务内执行。 你需要负责提交或回滚事务，并为其调用 Dispose ( # A1，还负责关闭并释放数据库连接。 例如：  

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

### <a name="clearing-up-the-transaction"></a>清除事务

可以将 null 传递到 UseTransaction ( # A1，以清除实体框架对当前事务的了解。 当你执行此操作时，实体框架不会提交或回滚现有事务，因此请谨慎使用，仅在你确定要执行的操作时使用。  

### <a name="errors-in-usetransaction"></a>UseTransaction 中的错误

如果在以下情况传递事务，则会在 UseTransaction ( # A1 中看到异常：  
- 实体框架已有一个现有的事务  
- 实体框架已在某一 TransactionScope 内运行  
- 传递的事务中的连接对象为 null。 也就是说，事务与连接无关-通常是该事务已完成的符号  
- 传递的事务中的连接对象与实体框架的连接不匹配。  

## <a name="using-transactions-with-other-features"></a>将事务与其他功能一起使用  

本部分详细说明了上述事务与的交互方式：  

- 连接复原  
- 异步方法  
- TransactionScope 交易  

### <a name="connection-resiliency"></a>连接复原  

新的连接复原功能不适用于用户启动的事务。 有关详细信息，请参阅 [重试执行策略](xref:ef6/fundamentals/connection-resiliency/retry-logic#user-initiated-transactions-are-not-supported)。  

### <a name="asynchronous-programming"></a>异步编程  

前面几节中所述的方法不需要更多选项或设置即可使用 [异步查询和保存方法](xref:ef6/fundamentals/async
)。 但请注意，根据您在异步方法中所执行的操作，这可能会导致长时间运行的事务，进而导致死锁或阻塞，导致整个应用程序的性能不佳。  

### <a name="transactionscope-transactions"></a>TransactionScope 交易  

在 EF6 之前，提供更大范围事务的建议方法是使用 TransactionScope 对象：  

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

SqlConnection 和实体框架都使用环境 TransactionScope 事务，因此一起提交。  

从 .NET 4.5.1 TransactionScope 开始，还更新为通过使用 [TransactionScopeAsyncFlowOption](https://msdn.microsoft.com/library/system.transactions.transactionscopeasyncflowoption.aspx) 枚举来处理异步方法：  

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

对于 TransactionScope 方法仍有一些限制：  

- 需要 .NET 4.5.1 或更高版本才能使用异步方法。  
- 除非你确定有且只有一个连接 (云方案不支持分布式事务) ，否则不能用于云方案。  
- 它不能与 UseTransaction ( # A1 方法结合前面的部分。  
- 如果你颁发了任何 DDL 并且尚未通过 MSDTC 服务启用分布式事务，则它会引发异常。  

TransactionScope 方法的优点：  

- 如果与同一事务中的另一个数据库建立了多个连接，则它会自动将本地事务升级到分布式事务，或将与一个数据库的连接组合到同一个事务中的不同数据库 (注意：您必须将 MSDTC 服务配置为允许此工作) 的分布式事务。  
- 易于编码。 如果你更愿意在后台隐式处理事务，而不是显式地在控制下进行处理，则 TransactionScope 方法可能更适合你。  

总之，在上面的 BeginTransaction ( # A1 和 UseTransaction ( # A3 Api 中，大多数用户不再需要 TransactionScope 方法。 如果继续使用 TransactionScope，请注意上述限制。 建议尽可能使用前面部分中所述的方法。  
