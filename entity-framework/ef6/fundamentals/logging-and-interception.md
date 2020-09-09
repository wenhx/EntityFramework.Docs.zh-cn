---
title: 记录和截取数据库操作-EF6
description: 日志记录和截取实体框架6中的数据库操作
author: divega
ms.date: 10/23/2016
ms.assetid: b5ee7eb1-88cc-456e-b53c-c67e24c3f8ca
uid: ef6/fundamentals/logging-and-interception
ms.openlocfilehash: bb5c3392b4f2e1f291d7ac373d07724f56d0eb30
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616200"
---
# <a name="logging-and-intercepting-database-operations"></a>记录和截取数据库操作
> [!NOTE]
> **仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。 如果使用的是早期版本，则部分或全部信息不适用。  

从实体框架6开始，无论何时实体框架将命令发送到数据库，都可以通过应用程序代码截获此命令。 这通常用于记录 SQL，但也可用于修改或中止命令。  

具体而言，EF 包括：  

- 与 DataContext 类似的上下文的日志属性。登录 LINQ to SQL  
- 用于自定义发送到日志的输出内容和格式的机制  
- 用于侦听的低级别构建基块，提供更好的控制/灵活性  

## <a name="context-log-property"></a>Context 日志属性  

可以将 DbContext 属性设置为采用字符串的任何方法的委托。 最常见的情况是，通过将其设置为该该设置的 "Write" 方法，将其与任何未任何类型一起使用。 当前上下文生成的所有 SQL 都将记录到该编写器。 例如，以下代码将 SQL 日志记录到控制台：  

``` csharp
using (var context = new BlogContext())
{
    context.Database.Log = Console.Write;

    // Your code here...
}
```  

请注意，上下文。数据库。日志设置为 Console。 这是将 SQL 日志记录到控制台所需的全部。  

让我们添加一些简单的查询/插入/更新代码，以便我们可以看到一些输出：  

``` csharp
using (var context = new BlogContext())
{
    context.Database.Log = Console.Write;

    var blog = context.Blogs.First(b => b.Title == "One Unicorn");

    blog.Posts.First().Title = "Green Eggs and Ham";

    blog.Posts.Add(new Post { Title = "I do not like them!" });

    context.SaveChangesAsync().Wait();
}
```  

这将生成以下输出：  

``` SQL
SELECT TOP (1)
    [Extent1].[Id] AS [Id],
    [Extent1].[Title] AS [Title]
    FROM [dbo].[Blogs] AS [Extent1]
    WHERE (N'One Unicorn' = [Extent1].[Title]) AND ([Extent1].[Title] IS NOT NULL)
-- Executing at 10/8/2013 10:55:41 AM -07:00
-- Completed in 4 ms with result: SqlDataReader

SELECT
    [Extent1].[Id] AS [Id],
    [Extent1].[Title] AS [Title],
    [Extent1].[BlogId] AS [BlogId]
    FROM [dbo].[Posts] AS [Extent1]
    WHERE [Extent1].[BlogId] = @EntityKeyValue1
-- EntityKeyValue1: '1' (Type = Int32)
-- Executing at 10/8/2013 10:55:41 AM -07:00
-- Completed in 2 ms with result: SqlDataReader

UPDATE [dbo].[Posts]
SET [Title] = @0
WHERE ([Id] = @1)
-- @0: 'Green Eggs and Ham' (Type = String, Size = -1)
-- @1: '1' (Type = Int32)
-- Executing asynchronously at 10/8/2013 10:55:41 AM -07:00
-- Completed in 12 ms with result: 1

INSERT [dbo].[Posts]([Title], [BlogId])
VALUES (@0, @1)
SELECT [Id]
FROM [dbo].[Posts]
WHERE @@ROWCOUNT > 0 AND [Id] = scope_identity()
-- @0: 'I do not like them!' (Type = String, Size = -1)
-- @1: '1' (Type = Int32)
-- Executing asynchronously at 10/8/2013 10:55:41 AM -07:00
-- Completed in 2 ms with result: SqlDataReader
```  

 (请注意，这是假设已发生任何数据库初始化的输出。 如果尚未发生数据库初始化，则会有更多的输出，显示所有工作迁移在这些涵盖下执行的操作，以检查或创建新数据库。 )   

## <a name="what-gets-logged"></a>记录了哪些内容？  

设置日志属性后，将记录以下所有内容：  

- 适用于所有不同类型的命令的 SQL。 例如：  
    - 查询，包括常规 LINQ 查询、eSQL 查询和来自 SqlQuery 等方法的原始查询  
    - 作为 SaveChanges 的一部分生成的插入、更新和删除操作  
    - 关系加载查询，如延迟加载生成的查询  
- 参数  
- 命令是否正在以异步方式执行  
- 指示命令开始执行的时间的时间戳  
- 命令是否已成功完成，引发异常，或是否已取消（对于异步）  
- 结果值的一些指示  
- 执行命令所花费的大致时间。 请注意，这是发送命令以返回结果对象的时间。 它不包含读取结果的时间。  

查看上面的示例输出，记录的四个命令中的每一个都是：  

- 通过调用上下文产生的查询。博客。首先  
    - 请注意，获取 SQL 的 ToString 方法不能处理此查询，因为 "First" 不提供可调用 ToString 的 IQueryable  
- 由博客的延迟加载引起的查询。创纪录  
    - 请注意延迟加载发生的键值的参数详细信息  
    - 仅记录设置为非默认值的参数的属性。 例如，仅当 Size 属性为非零时，才会显示该属性。  
- SaveChangesAsync 生成的两个命令;一项用于更改发布标题的更新，另一种用于添加新的帖子  
    - 请注意 FK 和 Title 属性的参数详细信息  
    - 请注意，这些命令是异步执行的  

## <a name="logging-to-different-places"></a>记录到不同的位置  

如上所示，记录到控制台非常简单。 通过使用不同 [类型的类型](https://msdn.microsoft.com/library/system.io.textwriter.aspx)，也可以很容易地记录到内存、文件等。  

如果你熟悉 LINQ to SQL 你可能会注意到，在 LINQ to SQL 日志属性设置为实际的 "设置" 对象时 (例如，Console。 Out) 而在 EF 中，Log 属性被设置为接受字符串 (的方法，例如，Console 或。 这种情况的原因是，通过接受可充当字符串接收器的任何委托，将 EF 从无效中分离。 例如，假设已有一些日志记录框架，并定义如下所示的日志记录方法：  

``` csharp
public class MyLogger
{
    public void Log(string component, string message)
    {
        Console.WriteLine("Component: {0} Message: {1} ", component, message);
    }
}
```  

此操作可能会挂接到 EF 日志属性，如下所示：  

``` csharp
var logger = new MyLogger();
context.Database.Log = s => logger.Log("EFApp", s);
```  

## <a name="result-logging"></a>结果日志记录  

默认记录器在将命令发送到数据库之前， (SQL) 、参数和 "执行" 行记录命令文本。 执行命令后，将记录包含运行时间的 "已完成" 行。  

请注意，对于异步命令，在异步任务实际完成、失败或取消之前，不会记录 "已完成" 行。  

"已完成" 行包含不同的信息，具体取决于命令的类型以及执行是否成功。  

### <a name="successful-execution"></a>成功执行  

对于成功完成的命令，输出为 "在 x 毫秒内完成，结果为："，后跟一些对结果的指示。 对于返回数据读取器的命令，结果指示是返回的 [DbDataReader](https://msdn.microsoft.com/library/system.data.common.dbdatareader.aspx) 的类型。 对于返回整数值的命令，如显示的结果上方显示的 update 命令，则为该整数。  

### <a name="failed-execution"></a>执行失败  

对于通过引发异常而失败的命令，输出包含来自异常的消息。 例如，使用 SqlQuery 对存在的表进行查询时，会生成如下所示的日志输出：  

``` SQL
SELECT * from ThisTableIsMissing
-- Executing at 5/13/2013 10:19:05 AM
-- Failed in 1 ms with error: Invalid object name 'ThisTableIsMissing'.
```  

### <a name="canceled-execution"></a>已取消执行  

对于取消任务的异步命令，结果可能会失败，并出现异常，因为这是当尝试取消时，基础 ADO.NET 提供程序经常执行的操作。 如果这种情况不会发生并且任务完全取消，则输出将如下所示：  

```console
update Blogs set Title = 'No' where Id = -1
-- Executing asynchronously at 5/13/2013 10:21:10 AM
-- Canceled in 1 ms
```  

## <a name="changing-log-content-and-formatting"></a>更改日志内容和格式  

在 "数据库概述" 下，使用 DatabaseLogFormatter 对象。 此对象将有效地绑定 IDbCommandInterceptor 实现 (参见下面的) 到接受字符串和 DbContext 的委托。 这意味着，DatabaseLogFormatter 上的方法是在执行命令之前和之后调用的。 这些 DatabaseLogFormatter 方法收集并格式化日志输出，并将其发送到委托。  

### <a name="customizing-databaselogformatter"></a>自定义 DatabaseLogFormatter  

可以通过创建从 DatabaseLogFormatter 派生的新类并根据需要重写方法，从而更改所记录的内容和格式。 要重写的最常见方法是：  

- LogCommand –重写此方法以更改命令在执行之前的记录方式。 默认情况下，LogCommand 为每个参数调用 LogParameter;您可以选择在重写中执行相同的操作，或以不同的方式处理参数。  
- LogResult –重写此方法以更改如何记录执行命令的结果。  
- LogParameter –重写此参数以更改参数日志记录的格式和内容。  

例如，假设我们想要在每个命令发送到数据库之前只记录一行。 可以通过两个替代来完成此操作：  

- 重写 LogCommand 以格式化和写入单一的 SQL 行  
- 重写 LogResult 不执行任何操作。  

代码将如下所示：

``` csharp
public class OneLineFormatter : DatabaseLogFormatter
{
    public OneLineFormatter(DbContext context, Action<string> writeAction)
        : base(context, writeAction)
    {
    }

    public override void LogCommand<TResult>(
        DbCommand command, DbCommandInterceptionContext<TResult> interceptionContext)
    {
        Write(string.Format(
            "Context '{0}' is executing command '{1}'{2}",
            Context.GetType().Name,
            command.CommandText.Replace(Environment.NewLine, ""),
            Environment.NewLine));
    }

    public override void LogResult<TResult>(
        DbCommand command, DbCommandInterceptionContext<TResult> interceptionContext)
    {
    }
}
```  

若要日志输出，只需调用写入方法，该方法会将输出发送到配置的写入委托。  

 (请注意，此代码只是为了举例地删除换行符。 它在查看复杂的 SQL 时可能无法正常运行。 )   

### <a name="setting-the-databaselogformatter"></a>设置 DatabaseLogFormatter  

创建新的 DatabaseLogFormatter 类后，需要将其注册到 EF。 这是使用基于代码的配置来完成的。 简而言之，这意味着要在与 DbContext 类相同的程序集中创建一个从 DbConfiguration 派生的新类，然后在此新类的构造函数中调用 SetDatabaseLogFormatter。 例如：  

``` csharp
public class MyDbConfiguration : DbConfiguration
{
    public MyDbConfiguration()
    {
        SetDatabaseLogFormatter(
            (context, writeAction) => new OneLineFormatter(context, writeAction));
    }
}
```  

### <a name="using-the-new-databaselogformatter"></a>使用新的 DatabaseLogFormatter  

此新 DatabaseLogFormatter 将在每次设置数据库时使用。 因此，运行第1部分中的代码将生成以下输出：  

```console
Context 'BlogContext' is executing command 'SELECT TOP (1) [Extent1].[Id] AS [Id], [Extent1].[Title] AS [Title]FROM [dbo].[Blogs] AS [Extent1]WHERE (N'One Unicorn' = [Extent1].[Title]) AND ([Extent1].[Title] IS NOT NULL)'
Context 'BlogContext' is executing command 'SELECT [Extent1].[Id] AS [Id], [Extent1].[Title] AS [Title], [Extent1].[BlogId] AS [BlogId]FROM [dbo].[Posts] AS [Extent1]WHERE [Extent1].[BlogId] = @EntityKeyValue1'
Context 'BlogContext' is executing command 'update [dbo].[Posts]set [Title] = @0where ([Id] = @1)'
Context 'BlogContext' is executing command 'insert [dbo].[Posts]([Title], [BlogId])values (@0, @1)select [Id]from [dbo].[Posts]where @@rowcount > 0 and [Id] = scope_identity()'
```  

## <a name="interception-building-blocks"></a>拦截构建基块  

到目前为止，我们已经介绍了如何使用 DbContext 记录由 EF 生成的 SQL。 但在某些低级别构建基块上，此代码实际上是相对较窄的外观，用于更常见的侦听。  

### <a name="interception-interfaces"></a>侦听接口  

拦截代码是围绕截取接口的概念构建的。 这些接口从 IDbInterceptor 继承，并定义在 EF 执行某个操作时调用的方法。 目的是要截获每种类型的对象的一个接口。 例如，IDbCommandInterceptor 接口定义在 EF 调用 ExecuteNonQuery、ExecuteScalar、ExecuteReader 和相关方法之前调用的方法。 同样，接口会定义在这些操作完成时调用的方法。 以上所示的 DatabaseLogFormatter 类实现此接口来记录命令。  

### <a name="the-interception-context"></a>截获上下文  

查看在任何侦听器接口上定义的方法很明显，就是每个调用都给定一个类型为 DbInterceptionContext 的对象，或是派生的某种类型的对象（如 DbCommandInterceptionContext） \<\> 。 此对象包含有关 EF 正在采取的操作的上下文信息。 例如，如果该操作是代表 DbContext 执行的，则 DbContext 将包含在 DbInterceptionContext 中。 同样，对于异步执行的命令，将在 DbCommandInterceptionContext 上设置 IsAsync 标志。  

### <a name="result-handling"></a>结果处理  

DbCommandInterceptionContext \<\> 类包含一个名为 Result、OriginalResult、Exception 和 OriginalException 的属性。 对于对在执行操作之前调用的截取方法的调用，这些属性将设置为 null/零（即，对于 .。。正在执行方法。 如果执行并成功执行该操作，则结果和 OriginalResult 将设置为操作的结果。 然后，可以在执行操作后调用的截取方法中观察这些值，即 .。。已执行方法。 同样，如果操作引发，则会设置 Exception 和 OriginalException 属性。  

#### <a name="suppressing-execution"></a>禁止执行  

如果侦听器在执行命令之前设置 Result 属性，则 (执行方法) ，EF 不会尝试实际执行该命令，而只是使用结果集。 换句话说，侦听器可以取消命令的执行，但会继续执行 EF，就像执行命令一样。  

通常使用包装提供程序执行的命令批处理就是使用此方法的示例。 侦听器将以批处理的形式存储命令以供以后执行，但会 "假设" 命令已正常执行。 请注意，它需要超过此数目才能实现批处理，但这是如何使用更改截取结果的示例。  

还可以通过在其中一个 "..."正在执行方法。 这会导致 EF 通过引发给定异常来继续执行操作。 当然，这可能会导致应用程序崩溃，但也可能是由 EF 处理的暂时性异常或其他异常。 例如，在命令执行失败时，可以在测试环境中使用它来测试应用程序的行为。  

#### <a name="changing-the-result-after-execution"></a>在执行后更改结果  

如果侦听器在执行命令后设置 Result 属性 (则在其中一个 .。。如果执行方法) 则 EF 将使用更改后的结果，而不是实际从操作返回的结果。 同样，如果侦听器在执行命令后设置了 Exception 属性，则 EF 将引发设置异常，就好像操作引发了异常一样。  

侦听器还可以将 Exception 属性设置为 null，以指示不应引发异常。 如果执行操作失败，则此方法会很有用，但侦听器希望 EF 继续操作，就像操作已成功一样。 这通常还涉及到设置结果，以便 EF 有一些结果值在继续时使用。  

#### <a name="originalresult-and-originalexception"></a>OriginalResult 和 OriginalException  

在 EF 执行了某一操作后，如果执行未通过，则将设置 Result 和 OriginalResult 属性，如果执行失败但出现异常，则将设置为 Exception 和 OriginalException 属性。  

OriginalResult 和 OriginalException 属性是只读的，并且仅在实际执行操作后由 EF 设置。 侦听器不能设置这些属性。 这意味着，任何侦听器都可以区分已由其他侦听器设置的异常或结果，而不是执行操作时所发生的实际异常或结果。  

### <a name="registering-interceptors"></a>正在注册侦听器  

一旦创建了一个或多个截获接口的类，就可以使用 DbInterception 类向 EF 注册它。 例如：  

``` csharp
DbInterception.Add(new NLogCommandInterceptor());
```  

还可以使用基于 DbConfiguration 代码的配置机制在应用域级别注册拦截程序。  

### <a name="example-logging-to-nlog"></a>示例：记录到 NLog  

让我们将这一切结合起来，使用 IDbCommandInterceptor 和 [NLog](https://nlog-project.org/) 来执行以下操作：  

- 为非异步执行的任何命令记录警告  
- 为执行时引发的任何命令记录错误  

下面是执行日志记录的类，应如下所示进行注册：  

``` csharp
public class NLogCommandInterceptor : IDbCommandInterceptor
{
    private static readonly Logger Logger = LogManager.GetCurrentClassLogger();

    public void NonQueryExecuting(
        DbCommand command, DbCommandInterceptionContext<int> interceptionContext)
    {
        LogIfNonAsync(command, interceptionContext);
    }

    public void NonQueryExecuted(
        DbCommand command, DbCommandInterceptionContext<int> interceptionContext)
    {
        LogIfError(command, interceptionContext);
    }

    public void ReaderExecuting(
        DbCommand command, DbCommandInterceptionContext<DbDataReader> interceptionContext)
    {
        LogIfNonAsync(command, interceptionContext);
    }

    public void ReaderExecuted(
        DbCommand command, DbCommandInterceptionContext<DbDataReader> interceptionContext)
    {
        LogIfError(command, interceptionContext);
    }

    public void ScalarExecuting(
        DbCommand command, DbCommandInterceptionContext<object> interceptionContext)
    {
        LogIfNonAsync(command, interceptionContext);
    }

    public void ScalarExecuted(
        DbCommand command, DbCommandInterceptionContext<object> interceptionContext)
    {
        LogIfError(command, interceptionContext);
    }

    private void LogIfNonAsync<TResult>(
        DbCommand command, DbCommandInterceptionContext<TResult> interceptionContext)
    {
        if (!interceptionContext.IsAsync)
        {
            Logger.Warn("Non-async command used: {0}", command.CommandText);
        }
    }

    private void LogIfError<TResult>(
        DbCommand command, DbCommandInterceptionContext<TResult> interceptionContext)
    {
        if (interceptionContext.Exception != null)
        {
            Logger.Error("Command {0} failed with exception {1}",
                command.CommandText, interceptionContext.Exception);
        }
    }
}
```  

请注意，此代码如何使用侦听上下文来发现何时以非异步方式执行命令，并在执行命令时发现错误。  
