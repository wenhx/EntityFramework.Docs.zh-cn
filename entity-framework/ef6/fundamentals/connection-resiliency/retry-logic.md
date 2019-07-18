---
title: 连接复原和重试逻辑-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 47d68ac1-927e-4842-ab8c-ed8c8698dff2
ms.openlocfilehash: a01216c3399ca4a04943563435eacd0047337a5f
ms.sourcegitcommit: c9c3e00c2d445b784423469838adc071a946e7c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68306578"
---
# <a name="connection-resiliency-and-retry-logic"></a>连接复原和重试逻辑
> [!NOTE]
> **仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。 如果使用的是早期版本，则部分或全部信息不适用。  

由于后端故障和网络不稳定, 连接到数据库服务器的应用程序始终容易遭受连接中断。 然而, 在基于 LAN 的环境中, 对于专用数据库服务器, 这些错误很少发生, 但通常不需要使用额外的逻辑来处理这些故障。 随着基于云的数据库服务器 (如 Microsoft Azure SQL 数据库) 的增长以及通过不太可靠的网络进行的连接, 现在更常见的连接中断。 这可能是因为云数据库使用防御性技术来确保服务的公平, 如连接限制, 或导致间歇超时和其他暂时性错误的网络不稳定。  

连接复原是指 EF 自动重试任何由于这些连接中断而失败的命令的功能。  

## <a name="execution-strategies"></a>执行策略  

连接重试由 IDbExecutionStrategy 接口的实现来处理。 IDbExecutionStrategy 的实现将负责接受一个操作, 如果发生异常, 则确定重试是否合适, 如果是, 则重试。 EF 附带四个执行策略:  

1. **DefaultExecutionStrategy**: 此执行策略不会重试任何操作, 它是 sql server 以外的数据库的默认值。  
2. **DefaultSqlExecutionStrategy**: 这是默认情况下使用的内部执行策略。 不过, 这种策略根本不会重试, 它将包装任何可能会暂时性的异常, 以通知用户他们可能想要启用连接复原。  
3. **DbExecutionStrategy**: 此类适用于其他执行策略 (包括你自己的自定义) 的基类。 它实现了一个指数重试策略, 其中, 初始重试会出现零延迟, 延迟将以指数方式递增, 直到达到最大重试计数。 此类有一个抽象 ShouldRetryOn 方法, 可在派生执行策略中实现该方法, 以控制应重试哪些异常。  
4. **SqlAzureExecutionStrategy**: 此执行策略继承自 DbExecutionStrategy, 并将在使用 Azure SQL 数据库时已知可能会暂时性的异常重试。

> [!NOTE]
> 执行策略2和4包含在 EF 附带的 Sql Server 提供程序中, 后者位于 EntityFramework 程序集中, 旨在与 SQL Server 配合使用。  

## <a name="enabling-an-execution-strategy"></a>启用执行策略  

告诉 EF 使用执行策略的最简单方法是使用[DbConfiguration](~/ef6/fundamentals/configuring/code-based.md)类的 SetExecutionStrategy 方法:  

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
    }
}
```  

此代码告知 EF 在连接到 SQL Server 时使用 SqlAzureExecutionStrategy。  

## <a name="configuring-the-execution-strategy"></a>配置执行策略  

SqlAzureExecutionStrategy 的构造函数可以接受两个参数: MaxRetryCount 和 MaxDelay。 MaxRetry count 是策略将重试的最大次数。 MaxDelay 是一个 TimeSpan, 表示执行策略将使用的重试之间的最大延迟。  

若要将最大重试次数设置为 1, 最大延迟为30秒, 请执行以下操作:  

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

第一次发生暂时性故障时, SqlAzureExecutionStrategy 会立即重试, 但会在每次重试之间延迟更长的时间, 直到超过最大重试限制或总时间达到最大延迟。  

执行策略只会重试有限数量的异常, 这些异常通常是暂时性的, 你仍需要处理其他错误, 并捕获 RetryLimitExceeded 异常, 以防错误不是暂时性的, 或者需要太长时间才能解决自动.  

使用重试执行策略时, 有一些已知的限制:  

## <a name="streaming-queries-are-not-supported"></a>不支持流式处理查询  

默认情况下, EF6 和更高版本将缓冲查询结果, 而不是流式传输。 如果希望流式传输结果, 可以使用 AsStreaming 方法将 LINQ to Entities 查询更改为流式处理。  

``` csharp
using (var db = new BloggingContext())
{
    var query = (from b in db.Blogs
                orderby b.Url
                select b).AsStreaming();
    }
}
```  

注册重试执行策略时, 不支持流式处理。 存在此限制的原因是, 连接可能会在返回的结果中下降。 出现这种情况时, EF 需要重新运行整个查询, 但无法可靠地了解已返回的结果 (数据自初始查询发送以来可能已更改, 结果可能会以不同的顺序返回, 结果可能不具有唯一标识符, 等等)。  

## <a name="user-initiated-transactions-are-not-supported"></a>不支持用户启动的事务  

配置导致重试的执行策略时, 对事务的使用存在一些限制。  

默认情况下, EF 将在事务中执行任何数据库更新。 不需要执行任何操作来启用此功能, EF 始终会自动执行此操作。  

例如, 在以下代码中, 将在事务中自动执行。 如果在插入新的一个站点后, SaveChanges 会失败, 则会回滚该事务, 并且不会将更改应用到数据库。 上下文还会处于状态, 该状态允许再次调用 SaveChanges 以重试应用更改。  

``` csharp
using (var db = new BloggingContext())
{
    db.Blogs.Add(new Site { Url = "http://msdn.com/data/ef" });
    db.Blogs.Add(new Site { Url = "http://blogs.msdn.com/adonet" });
    db.SaveChanges();
}
```  

如果不使用重试执行策略, 则可以在单个事务中包装多个操作。 例如, 下面的代码将两个 SaveChanges 调用包装在一个事务中。 如果任一操作的任何部分失败, 则不会应用任何更改。  

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

使用重试执行策略时不支持此操作, 因为 EF 不知道任何以前的操作以及如何重试。 例如, 如果第二个 SaveChanges 失败, 则 EF 不再具有重试第一个 SaveChanges 调用所需的信息。  

### <a name="workaround-suspend-execution-strategy"></a>解决方法：挂起执行策略  

一种可能的解决方法是针对需要使用用户启动的事务的代码段暂停重试执行策略。 执行此操作的最简单方法是将 SuspendExecutionStrategy 标志添加到基于代码的配置类, 并更改执行策略 lambda, 以在设置标志时返回默认 (非 retying) 执行策略。  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.SqlServer;
using System.Runtime.Remoting.Messaging;

namespace Demo
{
    public class MyConfiguration : DbConfiguration
    {
        public MyConfiguration()
        {
            this.SetExecutionStrategy("System.Data.SqlClient", () => SuspendExecutionStrategy
              ? (IDbExecutionStrategy)new DefaultExecutionStrategy()
              : new SqlAzureExecutionStrategy());
        }

        public static bool SuspendExecutionStrategy
        {
            get
            {
                return (bool?)CallContext.LogicalGetData("SuspendExecutionStrategy") ?? false;
            }
            set
            {
                CallContext.LogicalSetData("SuspendExecutionStrategy", value);
            }
        }
    }
}
```  

请注意, 我们使用 CallContext 存储标志值。 这为线程本地存储提供了类似的功能, 但可以安全地使用异步代码 (包括 async query) 并保存实体框架。  

现在, 我们可以挂起使用用户启动的事务的代码部分的执行策略。  

``` csharp
using (var db = new BloggingContext())
{
    MyConfiguration.SuspendExecutionStrategy = true;

    using (var trn = db.Database.BeginTransaction())
    {
        db.Blogs.Add(new Blog { Url = "http://msdn.com/data/ef" });
        db.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/adonet" });
        db.SaveChanges();

        db.Blogs.Add(new Blog { Url = "http://twitter.com/efmagicunicorns" });
        db.SaveChanges();

        trn.Commit();
    }

    MyConfiguration.SuspendExecutionStrategy = false;
}
```  

### <a name="workaround-manually-call-execution-strategy"></a>解决方法：手动调用执行策略  

另一种方法是手动使用执行策略, 并为其分配要运行的整个逻辑集, 以便在其中一个操作失败的情况下可以重试所有操作。 我们仍需要使用上面显示的方法挂起执行策略, 以便可重试代码块中使用的任何上下文都不会尝试重试。  

请注意, 应在代码块内构造要重试的所有上下文。 这可确保每次重试都从干净的状态开始。  

``` csharp
var executionStrategy = new SqlAzureExecutionStrategy();

MyConfiguration.SuspendExecutionStrategy = true;

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

MyConfiguration.SuspendExecutionStrategy = false;
```  
