---
title: 客户端与服务器评估 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 8b6697cc-7067-4dc2-8007-85d80503d123
uid: core/querying/client-eval
ms.openlocfilehash: 47e22be274d02b5221c638d07151d9607aa7e24f
ms.sourcegitcommit: 0d36e8ff0892b7f034b765b15e041f375f88579a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2018
ms.locfileid: "44250798"
---
# <a name="client-vs-server-evaluation"></a><span data-ttu-id="ae07f-102">客户端与服务器评估</span><span class="sxs-lookup"><span data-stu-id="ae07f-102">Client vs. Server Evaluation</span></span>

<span data-ttu-id="ae07f-103">Entity Framework Core 支持部分查询在客户端上求值，而将其他部分推送到数据库执行。</span><span class="sxs-lookup"><span data-stu-id="ae07f-103">Entity Framework Core supports parts of the query being evaluated on the client and parts of it being pushed to the database.</span></span> <span data-ttu-id="ae07f-104">由数据库提供程序确定查询的哪些部分会在数据库中求值。</span><span class="sxs-lookup"><span data-stu-id="ae07f-104">It is up to the database provider to determine which parts of the query will be evaluated in the database.</span></span>

> [!TIP]  
> <span data-ttu-id="ae07f-105">可在 GitHub 上查看此文章的[示例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)。</span><span class="sxs-lookup"><span data-stu-id="ae07f-105">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="client-evaluation"></a><span data-ttu-id="ae07f-106">客户端求值</span><span class="sxs-lookup"><span data-stu-id="ae07f-106">Client evaluation</span></span>

<span data-ttu-id="ae07f-107">在下面的示例中，一个辅助方法用于标准化从 SQL Server 数据库中返回的博客的 URL。</span><span class="sxs-lookup"><span data-stu-id="ae07f-107">In the following example a helper method is used to standardize URLs for blogs that are returned from a SQL Server database.</span></span> <span data-ttu-id="ae07f-108">由于 SQL Server 提供程序不了解此方法的实现方式，因此不可以将其转换为 SQL。</span><span class="sxs-lookup"><span data-stu-id="ae07f-108">Because the SQL Server provider has no insight into how this method is implemented, it is not possible to translate it into SQL.</span></span> <span data-ttu-id="ae07f-109">除了在客户端上是通过执行该方法来返回 `URL`，查询的其余部分都是在数据库中执行的。</span><span class="sxs-lookup"><span data-stu-id="ae07f-109">All other aspects of the query are evaluated in the database, but passing the returned `URL` through this method is performed on the client.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/ClientEval/Sample.cs?highlight=6)] -->
``` csharp
var blogs = context.Blogs
    .OrderByDescending(blog => blog.Rating)
    .Select(blog => new
    {
        Id = blog.BlogId,
        Url = StandardizeUrl(blog.Url)
    })
    .ToList();
```

<!-- [!code-csharp[Main](samples/core/Querying/Querying/ClientEval/Sample.cs)] -->
``` csharp
public static string StandardizeUrl(string url)
{
    url = url.ToLower();

    if (!url.StartsWith("http://"))
    {
        url = string.Concat("http://", url);
    }

    return url;
}
```

## <a name="client-evaluation-performance-issues"></a><span data-ttu-id="ae07f-110">客户端求值性能问题</span><span class="sxs-lookup"><span data-stu-id="ae07f-110">Client evaluation performance issues</span></span>

<span data-ttu-id="ae07f-111">虽然客户端求值非常有用，但在某些情况下可能会导致性能不佳。</span><span class="sxs-lookup"><span data-stu-id="ae07f-111">While client evaluation can be very useful, in some instances it can result in poor performance.</span></span> <span data-ttu-id="ae07f-112">请考虑以下查询，其中辅助方法在筛选器中使用。</span><span class="sxs-lookup"><span data-stu-id="ae07f-112">Consider the following query, where the helper method is now used in a filter.</span></span> <span data-ttu-id="ae07f-113">由于无法在数据库中执行此操作，因此所有数据将被拉入内存中，然后会在客户端上应用筛选器。</span><span class="sxs-lookup"><span data-stu-id="ae07f-113">Because this can't be performed in the database, all the data is pulled into memory and then the filter is applied on the client.</span></span> <span data-ttu-id="ae07f-114">根据数据量以及筛选出的数据量，这可能会导致性能低下。</span><span class="sxs-lookup"><span data-stu-id="ae07f-114">Depending on the amount of data, and how much of that data is filtered out, this could result in poor performance.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/ClientEval/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .Where(blog => StandardizeUrl(blog.Url).Contains("dotnet"))
    .ToList();
```

## <a name="client-evaluation-logging"></a><span data-ttu-id="ae07f-115">客户端求值日志记录</span><span class="sxs-lookup"><span data-stu-id="ae07f-115">Client evaluation logging</span></span>

<span data-ttu-id="ae07f-116">默认情况下，当执行客户端求值时，EF Core 将记录警告。</span><span class="sxs-lookup"><span data-stu-id="ae07f-116">By default, EF Core will log a warning when client evaluation is performed.</span></span> <span data-ttu-id="ae07f-117">有关查看日志记录输出的详细信息，请参阅[日志记录](../miscellaneous/logging.md)。</span><span class="sxs-lookup"><span data-stu-id="ae07f-117">See [Logging](../miscellaneous/logging.md) for more information on viewing logging output.</span></span> 

## <a name="optional-behavior-throw-an-exception-for-client-evaluation"></a><span data-ttu-id="ae07f-118">可选行为：客户端求值引发异常</span><span class="sxs-lookup"><span data-stu-id="ae07f-118">Optional behavior: throw an exception for client evaluation</span></span>

<span data-ttu-id="ae07f-119">我们可以将执行客户端求值时记录警告的默认行为改为引发异常或不执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="ae07f-119">You can change the behavior when client evaluation occurs to either throw or do nothing.</span></span> <span data-ttu-id="ae07f-120">这是在为上下文设置选项时完成的（通常在 `DbContext.OnConfiguring` 中完成，如果使用的是 ASP.NET Core，则在 `Startup.cs` 中完成）。</span><span class="sxs-lookup"><span data-stu-id="ae07f-120">This is done when setting up the options for your context - typically in `DbContext.OnConfiguring`, or in `Startup.cs` if you are using ASP.NET Core.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/ClientEval/ThrowOnClientEval/BloggingContext.cs?highlight=5)] -->
``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFQuerying;Trusted_Connection=True;")
        .ConfigureWarnings(warnings => warnings.Throw(RelationalEventId.QueryClientEvaluationWarning));
}
```
