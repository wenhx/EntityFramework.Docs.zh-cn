---
title: 原生 SQL 查询 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 70aae9b5-8743-4557-9c5d-239f688bf418
uid: core/querying/raw-sql
ms.openlocfilehash: 91592ea9f7c73f10446993282c1874c852000871
ms.sourcegitcommit: c9c3e00c2d445b784423469838adc071a946e7c9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68306549"
---
# <a name="raw-sql-queries"></a><span data-ttu-id="09423-102">原生 SQL 查询</span><span class="sxs-lookup"><span data-stu-id="09423-102">Raw SQL Queries</span></span>

<span data-ttu-id="09423-103">通过 Entity Framework Core 可以在使用关系数据库时下降到原始 SQL 查询。</span><span class="sxs-lookup"><span data-stu-id="09423-103">Entity Framework Core allows you to drop down to raw SQL queries when working with a relational database.</span></span> <span data-ttu-id="09423-104">这在无法使用 LINQ 表达要执行的查询，或因使用 LINQ 查询而导致低效的 SQL 查询时非常有用。</span><span class="sxs-lookup"><span data-stu-id="09423-104">This can be useful if the query you want to perform can't be expressed using LINQ, or if using a LINQ query is resulting in inefficient SQL queries.</span></span> <span data-ttu-id="09423-105">原始 SQL 查询可返回实体类型，或者，从 EF Core 2.1 开始，可返回模型中的[查询类型](xref:core/modeling/query-types)。</span><span class="sxs-lookup"><span data-stu-id="09423-105">Raw SQL queries can return entity types or, starting with EF Core 2.1, [query types](xref:core/modeling/query-types) that are part of your model.</span></span>

> [!TIP]  
> <span data-ttu-id="09423-106">可在 GitHub 上查看此文章的[示例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)。</span><span class="sxs-lookup"><span data-stu-id="09423-106">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="basic-raw-sql-queries"></a><span data-ttu-id="09423-107">基本原生 SQL 查询</span><span class="sxs-lookup"><span data-stu-id="09423-107">Basic raw SQL queries</span></span>

<span data-ttu-id="09423-108">可以使用 *FromSql* 扩展方法基于原生 SQL 查询开始 LINQ 查询。</span><span class="sxs-lookup"><span data-stu-id="09423-108">You can use the *FromSql* extension method to begin a LINQ query based on a raw SQL query.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSql("SELECT * FROM dbo.Blogs")
    .ToList();
```

<span data-ttu-id="09423-109">原生 SQL 查询可用于执行存储过程。</span><span class="sxs-lookup"><span data-stu-id="09423-109">Raw SQL queries can be used to execute a stored procedure.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogs")
    .ToList();
```

## <a name="passing-parameters"></a><span data-ttu-id="09423-110">传递参数</span><span class="sxs-lookup"><span data-stu-id="09423-110">Passing parameters</span></span>

<span data-ttu-id="09423-111">正如接受 SQL 的任何 API 一样，务必参数化任何用户输入以抵御 SQL 注入攻击。</span><span class="sxs-lookup"><span data-stu-id="09423-111">As with any API that accepts SQL, it is important to parameterize any user input to protect against a SQL injection attack.</span></span> <span data-ttu-id="09423-112">可以将参数占位符包含在 SQL 查询字符串中，然后提供参数值作为其他参数。</span><span class="sxs-lookup"><span data-stu-id="09423-112">You can include parameter placeholders in the SQL query string and then supply parameter values as additional arguments.</span></span> <span data-ttu-id="09423-113">提供的任何参数值将自动转换为 `DbParameter`。</span><span class="sxs-lookup"><span data-stu-id="09423-113">Any parameter values you supply will automatically be converted to a `DbParameter`.</span></span>

<span data-ttu-id="09423-114">下面的示例将一个参数传递到存储过程。</span><span class="sxs-lookup"><span data-stu-id="09423-114">The following example passes a single parameter to a stored procedure.</span></span> <span data-ttu-id="09423-115">尽管这看上去可能像 `String.Format` 语法，但提供的值包装在参数中，且生成的参数名称插入在指定 `{0}` 占位符的位置。</span><span class="sxs-lookup"><span data-stu-id="09423-115">While this may look like `String.Format` syntax, the supplied value is wrapped in a parameter and the generated parameter name inserted where the `{0}` placeholder was specified.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogsForUser {0}", user)
    .ToList();
```

<span data-ttu-id="09423-116">这是相同的查询，但使用 EF Core 2.0 及更高版本支持的字符串内插语法：</span><span class="sxs-lookup"><span data-stu-id="09423-116">This is the same query but using string interpolation syntax, which is supported in EF Core 2.0 and above:</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSql($"EXECUTE dbo.GetMostPopularBlogsForUser {user}")
    .ToList();
```

<span data-ttu-id="09423-117">你还可构造 DbParameter 并将其作为参数值提供：</span><span class="sxs-lookup"><span data-stu-id="09423-117">You can also construct a DbParameter and supply it as a parameter value:</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = new SqlParameter("user", "johndoe");

var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogsForUser @user", user)
    .ToList();
```

<span data-ttu-id="09423-118">这样，你就能在 SQL 查询字符串中使用已命名的参数，这在存储的流程具有可选参数时非常有用：</span><span class="sxs-lookup"><span data-stu-id="09423-118">This allows you to use named parameters in the SQL query string, which is useful when a stored procedure has optional parameters:</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = new SqlParameter("user", "johndoe");

var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogs @filterByUser=@user", user)
    .ToList();
```

## <a name="composing-with-linq"></a><span data-ttu-id="09423-119">使用 LINQ 编写</span><span class="sxs-lookup"><span data-stu-id="09423-119">Composing with LINQ</span></span>

<span data-ttu-id="09423-120">如果发送到数据库中的 SQL 查询是可组合的，则可以在原始 SQL 查询后面紧跟着使用 LINQ 运算符。</span><span class="sxs-lookup"><span data-stu-id="09423-120">If the SQL query can be composed on in the database, then you can compose on top of the initial raw SQL query using LINQ operators.</span></span> <span data-ttu-id="09423-121">以 `SELECT` 关键字开始的 SQL 查询一般是可组合的。</span><span class="sxs-lookup"><span data-stu-id="09423-121">SQL queries that can be composed on begin with the `SELECT` keyword.</span></span>

<span data-ttu-id="09423-122">下面的示例使用原始 SQL 从一个表值函数 (Table-Valued Function，TVF) 中进行查询，然后结合使用 LINQ 在其上进行筛选和排序。</span><span class="sxs-lookup"><span data-stu-id="09423-122">The following example uses a raw SQL query that selects from a Table-Valued Function (TVF) and then composes on it using LINQ to perform filtering and sorting.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSql($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Where(b => b.Rating > 3)
    .OrderByDescending(b => b.Rating)
    .ToList();
```

## <a name="change-tracking"></a><span data-ttu-id="09423-123">更改跟踪</span><span class="sxs-lookup"><span data-stu-id="09423-123">Change Tracking</span></span>

<span data-ttu-id="09423-124">使用 `FromSql()` 的查询遵循与 EF Core 中所有其他 LINQ 查询完全相同的更改跟踪规则。</span><span class="sxs-lookup"><span data-stu-id="09423-124">Queries that use the `FromSql()` follow the exact same change tracking rules as any other LINQ query in EF Core.</span></span> <span data-ttu-id="09423-125">例如，如果该查询投影实体类型，默认情况下会跟踪结果。</span><span class="sxs-lookup"><span data-stu-id="09423-125">For example, if the query projects entity types, the results will be tracked by default.</span></span>  

<span data-ttu-id="09423-126">下面的示例使用原始 SQL 查询，该查询从表值函数 (TVF) 中进行选择，然后禁用通过对 .AsNoTracking() 的调用来更改跟踪：</span><span class="sxs-lookup"><span data-stu-id="09423-126">The following example uses a raw SQL query that selects from a Table-Valued Function (TVF), then disables change tracking with the call to .AsNoTracking():</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Query<SearchBlogsDto>()
    .FromSql($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .AsNoTracking()
    .ToList();
```

## <a name="including-related-data"></a><span data-ttu-id="09423-127">包含关联数据</span><span class="sxs-lookup"><span data-stu-id="09423-127">Including related data</span></span>

<span data-ttu-id="09423-128">`Include()` 方法可用于添加相关数据，就像对其他 LINQ 查询那样：</span><span class="sxs-lookup"><span data-stu-id="09423-128">The `Include()` method can be used to include related data, just like with any other LINQ query:</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSql($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Include(b => b.Posts)
    .ToList();
```

## <a name="limitations"></a><span data-ttu-id="09423-129">限制</span><span class="sxs-lookup"><span data-stu-id="09423-129">Limitations</span></span>

<span data-ttu-id="09423-130">使用原生 SQL 查询时需注意以下几个限制：</span><span class="sxs-lookup"><span data-stu-id="09423-130">There are a few limitations to be aware of when using raw SQL queries:</span></span>

* <span data-ttu-id="09423-131">SQL 查询必须返回实体或查询类型的所有属性的数据。</span><span class="sxs-lookup"><span data-stu-id="09423-131">The SQL query must return data for all properties of the entity or query type.</span></span>

* <span data-ttu-id="09423-132">结果集中的列名必须与属性映射到的列名称匹配。</span><span class="sxs-lookup"><span data-stu-id="09423-132">The column names in the result set must match the column names that properties are mapped to.</span></span> <span data-ttu-id="09423-133">请注意，这与 EF6 不同，EF6 中忽略了原始 SQL 查询时的属性/列映射关系，只需结果集列名与属性名相匹配即可。</span><span class="sxs-lookup"><span data-stu-id="09423-133">Note this is different from EF6 where property/column mapping was ignored for raw SQL queries and result set column names had to match the property names.</span></span>

* <span data-ttu-id="09423-134">SQL 查询不能包含关联数据。</span><span class="sxs-lookup"><span data-stu-id="09423-134">The SQL query cannot contain related data.</span></span> <span data-ttu-id="09423-135">但是，在许多情况下你可以在查询后面紧跟着使用 `Include` 方法以返回关联数据（请参阅[包含关联数据](#including-related-data)）。</span><span class="sxs-lookup"><span data-stu-id="09423-135">However, in many cases you can compose on top of the query using the `Include` operator to return related data (see [Including related data](#including-related-data)).</span></span>

* <span data-ttu-id="09423-136">传递到此方法的 `SELECT` 语句通常应该是可组合的：如果 EF Core 需要在服务器端计算更多查询运算符（例如，转换 `FromSql` 后跟的 LINQ 运算符），所提供的 SQL 会被视为子查询。</span><span class="sxs-lookup"><span data-stu-id="09423-136">`SELECT` statements passed to this method should generally be composable: If EF Core needs to evaluate additional query operators on the server (for example, to translate LINQ operators applied after `FromSql`), the supplied SQL will be treated as a subquery.</span></span> <span data-ttu-id="09423-137">这意味着传递的 SQL 不应包含子查询上无效的任何字符或选项，如：</span><span class="sxs-lookup"><span data-stu-id="09423-137">This means that the SQL passed should not contain any characters or options that are not valid on a subquery, such as:</span></span>
  * <span data-ttu-id="09423-138">结尾分号</span><span class="sxs-lookup"><span data-stu-id="09423-138">a trailing semicolon</span></span>
  * <span data-ttu-id="09423-139">在 SQL Server 上，结尾处的查询级提示（例如，`OPTION (HASH JOIN)`）</span><span class="sxs-lookup"><span data-stu-id="09423-139">On SQL Server, a trailing query-level hint (for example, `OPTION (HASH JOIN)`)</span></span>
  * <span data-ttu-id="09423-140">在 SQL Server 上，`SELECT` 子句中不带 `OFFSET 0` 或 `TOP 100 PERCENT` 的 `ORDER BY` 子句</span><span class="sxs-lookup"><span data-stu-id="09423-140">On SQL Server, an `ORDER BY` clause that is not accompanied of `OFFSET 0` OR `TOP 100 PERCENT` in the `SELECT` clause</span></span>

* <span data-ttu-id="09423-141">除 `SELECT` 以外的其他 SQL 语句自动识别为不可编写。</span><span class="sxs-lookup"><span data-stu-id="09423-141">SQL statements other than `SELECT` are recognized automatically as non-composable.</span></span> <span data-ttu-id="09423-142">因此，存储过程的完整结果将始终返回到客户端，且在内存中计算 `FromSql` 后应用的任何 LINQ 运算符。</span><span class="sxs-lookup"><span data-stu-id="09423-142">As a consequence, the full results of stored procedures are always returned to the client and any LINQ operators applied after `FromSql` are evaluated in-memory.</span></span>

> [!WARNING]  
> <span data-ttu-id="09423-143">**始终对原始 SQL 查询使用参数化：** 除了验证用户输入，还应始终为原始 SQL 查询或命令中使用的任何值启用参数化传值机制。</span><span class="sxs-lookup"><span data-stu-id="09423-143">**Always use parameterization for raw SQL queries:** In addition to validating user input, always use parameterization for any values used in a raw SQL query/command.</span></span> <span data-ttu-id="09423-144">使用接受原始 SQL 字符串（如 `FromSql` 和 `ExecuteSqlCommand`）的 API，可以轻松地将值作为参数传递。</span><span class="sxs-lookup"><span data-stu-id="09423-144">APIs that accept a raw SQL string such as `FromSql` and `ExecuteSqlCommand` allow values to be easily passed as parameters.</span></span> <span data-ttu-id="09423-145">接受 FormattableString 的 `FromSql` 和 `ExecuteSqlCommand` 的重载还允许使用字符串内插语法，以帮助防止 SQL 注入攻击。</span><span class="sxs-lookup"><span data-stu-id="09423-145">Overloads of `FromSql` and `ExecuteSqlCommand` that accept FormattableString also allow using string interpolation syntax in a way that helps protect against SQL injection attacks.</span></span> 
> 
> <span data-ttu-id="09423-146">如果你正在使用字符串连接或内插来动态构建查询字符串的任何部分，或者将用户输入传递给可以将这些输入作为动态 SQL 执行的语句或存储过程，那么你将负责验证任何输入，以防止 SQL 注入攻击。</span><span class="sxs-lookup"><span data-stu-id="09423-146">If you are using string concatenation or interpolation to dynamically build any part of the query string, or passing user input to statements or stored procedures that can execute those inputs as dynamic SQL, then you are responsible for validating any input to protect against SQL injection attacks.</span></span>
