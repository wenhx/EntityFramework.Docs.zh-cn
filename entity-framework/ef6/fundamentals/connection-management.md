---
title: 连接管理-EF6
description: 实体框架6中的连接管理
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/connection-management
ms.openlocfilehash: e2097f5ed0471d309292625990cc5f9a598c5b2a
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070557"
---
# <a name="connection-management"></a>连接管理
本页介绍实体框架与将连接传递到上下文以及 **连接到 ( # B1 ** API 的功能的情况。  

## <a name="passing-connections-to-the-context"></a>将连接传递到上下文  

### <a name="behavior-for-ef5-and-earlier-versions"></a>EF5 及更早版本的行为  

有两个接受连接的构造函数：  

``` csharp
public DbContext(DbConnection existingConnection, bool contextOwnsConnection)
public DbContext(DbConnection existingConnection, DbCompiledModel model, bool contextOwnsConnection)
```  

可以使用这些方法，但必须解决几个限制：  

1. 如果将打开的连接传递到其中的任何一个连接，则在框架第一次尝试使用它时，将引发 InvalidOperationException，指出它无法重新打开已打开的连接。  
2. ContextOwnsConnection 标志解释为在释放上下文时是否应释放基础存储连接。 但无论此设置如何，在释放上下文时，存储连接始终关闭。 因此，如果有多个具有相同连接的 DbContext 在第一次处理上下文时将关闭连接 (同样，如果已使用 DbContext 混合了现有 ADO.NET 连接，DbContext 将始终在) 释放时关闭连接。  

通过传递关闭的连接并仅执行在创建所有上下文后将打开该连接的代码，可以绕过上述首个限制：  

``` csharp
using System.Collections.Generic;
using System.Data.Common;
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.EntityClient;
using System.Linq;

namespace ConnectionManagementExamples
{
    class ConnectionManagementExampleEF5
    {         
        public static void TwoDbContextsOneConnection()
        {
            using (var context1 = new BloggingContext())
            {
                var conn =
                    ((EntityConnection)  
                        ((IObjectContextAdapter)context1).ObjectContext.Connection)  
                            .StoreConnection;

                using (var context2 = new BloggingContext(conn, contextOwnsConnection: false))
                {
                    context2.Database.ExecuteSqlCommand(
                        @"UPDATE Blogs SET Rating = 5" +
                        " WHERE Name LIKE '%Entity Framework%'");

                    var query = context1.Posts.Where(p => p.Blog.Rating > 5);
                    foreach (var post in query)
                    {
                        post.Title += "[Cool Blog]";
                    }
                    context1.SaveChanges();
                }
            }
        }
    }
}
```  

第二个限制是指您需要避免释放任何 DbContext 对象，直到您已准备好连接才能关闭。  

### <a name="behavior-in-ef6-and-future-versions"></a>EF6 和未来版本中的行为  

在 EF6 和未来的版本中，DbContext 具有相同的两个构造函数，但不再要求在收到时传递到构造函数的连接被关闭。 现在可以这样做：  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
using System.Transactions;

namespace ConnectionManagementExamples
{
    class ConnectionManagementExample
    {
        public static void PassingAnOpenConnection()
        {
            using (var conn = new SqlConnection("{connectionString}"))
            {
                conn.Open();

                var sqlCommand = new SqlCommand();
                sqlCommand.Connection = conn;
                sqlCommand.CommandText =
                    @"UPDATE Blogs SET Rating = 5" +
                     " WHERE Name LIKE '%Entity Framework%'";
                sqlCommand.ExecuteNonQuery();

                using (var context = new BloggingContext(conn, contextOwnsConnection: false))
                {
                    var query = context.Posts.Where(p => p.Blog.Rating > 5);
                    foreach (var post in query)
                    {
                        post.Title += "[Cool Blog]";
                    }
                    context.SaveChanges();
                }

                var sqlCommand2 = new SqlCommand();
                sqlCommand2.Connection = conn;
                sqlCommand2.CommandText =
                    @"UPDATE Blogs SET Rating = 7" +
                     " WHERE Name LIKE '%Entity Framework Rocks%'";
                sqlCommand2.ExecuteNonQuery();
            }
        }
    }
}
```  

此外，contextOwnsConnection 标志现在控制在释放 DbContext 时是否关闭并释放连接。 因此，在上述示例中，当上下文被释放时，连接将不会关闭 (第32行) ，因为它已在 EF 的以前版本中，而是在 (行 40) 释放连接本身。  

当然，DbContext 仍有可能控制连接 (只需将 contextOwnsConnection 设置为 true，或者使用其他构造函数之一) 如果需要。  

> [!NOTE]
> 在此新模型中使用事务时，还有一些其他注意事项。 有关详细信息，请参阅使用 [事务](xref:ef6/saving/transactions)。  

## <a name="databaseconnectionopen"></a>打开 ( # A1  

### <a name="behavior-for-ef5-and-earlier-versions"></a>EF5 及更早版本的行为  

在 EF5 及更早版本中，存在一个 bug，以便不会更新 **ObjectContext** ，以反映基础存储连接的真实状态。 例如，如果执行了下面的代码，则可以将状态视为 **已关闭** ，即使基础存储连接已 **打开**。  

``` csharp
((IObjectContextAdapter)context).ObjectContext.Connection.State
```  

另外，如果您通过调用 ( # A1 打开数据库连接，则在下一次执行查询或调用需要数据库连接的任何内容 (例如，SaveChanges ( # A4 # A5 后，基础存储连接将关闭。 然后，上下文将重新打开并在每次需要另一数据库操作时重新关闭连接：  

``` csharp
using System;
using System.Data;
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.EntityClient;

namespace ConnectionManagementExamples
{
    public class DatabaseOpenConnectionBehaviorEF5
    {
        public static void DatabaseOpenConnectionBehavior()
        {
            using (var context = new BloggingContext())
            {
                // At this point the underlying store connection is closed

                context.Database.Connection.Open();

                // Now the underlying store connection is open  
                // (though ObjectContext.Connection.State will report closed)

                var blog = new Blog { /* Blog’s properties */ };
                context.Blogs.Add(blog);

                // The underlying store connection is still open  

                context.SaveChanges();

                // After SaveChanges() the underlying store connection is closed  
                // Each SaveChanges() / query etc now opens and immediately closes
                // the underlying store connection

                blog = new Blog { /* Blog’s properties */ };
                context.Blogs.Add(blog);
                context.SaveChanges();
            }
        }
    }
}
```  

### <a name="behavior-in-ef6-and-future-versions"></a>EF6 和未来版本中的行为  

对于 EF6 和更早的版本，如果调用代码选择通过调用上下文来打开连接，我们采用了这种方法。如果打开 ( # A1，则它有很好的理由这样做，框架将假定它要控制连接的打开和关闭，并且将不再自动关闭连接。  

> [!NOTE]
> 这可能会导致连接长时间处于打开状态，因此请谨慎使用。  

我们还更新了代码，使 ObjectContext 连接状态立即跟踪基础连接的状态。  

``` csharp
using System;
using System.Data;
using System.Data.Entity;
using System.Data.Entity.Core.EntityClient;
using System.Data.Entity.Infrastructure;

namespace ConnectionManagementExamples
{
    internal class DatabaseOpenConnectionBehaviorEF6
    {
        public static void DatabaseOpenConnectionBehavior()
        {
            using (var context = new BloggingContext())
            {
                // At this point the underlying store connection is closed

                context.Database.Connection.Open();

                // Now the underlying store connection is open and the
                // ObjectContext.Connection.State correctly reports open too

                var blog = new Blog { /* Blog’s properties */ };
                context.Blogs.Add(blog);
                context.SaveChanges();

                // The underlying store connection remains open for the next operation  

                blog = new Blog { /* Blog’s properties */ };
                context.Blogs.Add(blog);
                context.SaveChanges();

                // The underlying store connection is still open

           } // The context is disposed – so now the underlying store connection is closed
        }
    }
}
```  
